# Production Readiness Plan — EU→CIS Concierge Delivery Platform

## Context

Two repositories make up the product:
- **`delivery-service`** — Django 6 / DRF backend + Unfold admin + server-rendered landing. Solid service layer (`delivery/services/*`) and test suite (`delivery/tests/*`). Currently on SQLite, `DatabaseCache`, Gunicorn behind nothing, deployed via Docker Compose to a self-hosted runner.
- **`mobile-ds`** — Flutter / Riverpod / GoRouter app. **Only `lib/` exists — there are no `android/`, `ios/`, or `web/` platform folders, no `test/`, and no Firebase config.** The app cannot currently be built or released.

A full QA audit (`bugs` file) flagged 18 issues (C-1…L-6); all have been fixed and committed. The backend CI test suite was just brought green. The goal now is to take both apps from "audit-clean MVP" to a **full production release**.

**Decisions locked with the user:**
- Infra: **single VPS, Docker Compose** (add Postgres/Redis/nginx as containers later as needed).
- Database: **stay on SQLite during stabilization**; migrate to **PostgreSQL later**, only after all problems are fixed and the entire test suite is green (Phase 4 — gated).
- Mobile: **public app stores** (Google Play + Apple App Store).
- New capabilities to build: **payment gateway, Email/SMTP, server-side push (FCM)**.

This plan is a phased roadmap. Phases 0–1 are prerequisites; 2–3 deliver the new capabilities and a shippable app; 4 is the gated DB migration; 5 is launch/ops. The deliverable artifact is `PLAN.md` written to the projects root.

---

## Phase 0 — Stabilization gate (must be green before anything else)

**Backend**
- Confirm full suite green: `DJANGO_DEBUG=true DJANGO_SETTINGS_MODULE=config.test_settings python manage.py test delivery.tests` (plus legacy `delivery/test_*.py`).
- Run `python manage.py check --deploy` and record the baseline warnings (drives Phase 1).

**Mobile (critical — currently unbuildable)**
- Generate platform scaffolding: `flutter create . --platforms=android,ios` (preserves `lib/`, `pubspec.yaml`).
- `flutter pub get` → `flutter analyze` clean → `flutter build apk --debug` succeeds with `--dart-define=API_BASE_URL=...`.
- Add a minimal `test/` with smoke widget tests (app boots, login screen renders, router redirects unauth→/login).

**Exit criteria:** both repos build; backend tests + `flutter analyze` + mobile smoke tests pass in CI.

---

## Phase 1 — Backend production hardening (single VPS)

Files: `config/settings.py`, `docker-compose.yml`, `entrypoint.sh`, `.env.example`, new `nginx/` + `config/settings_prod` knobs (env-driven).

1. **Security settings** (gate: `check --deploy` clean when `DEBUG=False`):
   - `SECURE_SSL_REDIRECT=True`, `SECURE_HSTS_SECONDS` (+ include-subdomains/preload), `SESSION_COOKIE_SECURE=True`, `CSRF_COOKIE_SECURE=True`, `SECURE_CONTENT_TYPE_NOSNIFF`, `X_FRAME_OPTIONS=DENY`.
   - Make `CSRF_TRUSTED_ORIGINS` env-driven (currently hardcoded at `settings.py:61-68`); same pattern already used for `ALLOWED_HOSTS`.
2. **Secrets hygiene:** remove hardcoded `DJANGO_ADMIN_PASSWORD=admin123`, `DJANGO_SECRET_KEY`, and `admin@local.dev` from `docker-compose.yml`; load from `.env`/Docker secrets. Set `MOCK_DATA_ENABLED=false`, `DJANGO_DEBUG=false` in the prod compose.
3. **Reverse proxy + TLS:** add an `nginx` service terminating TLS (Let's Encrypt/certbot), proxying to Gunicorn, serving `/static` & `/media`, and applying an edge `limit_req` on `/api/v1/auth/*` (defence-in-depth atop DRF throttles).
4. **Backups:** cron-driven DB dump (SQLite file copy now; `pg_dump` after Phase 4) + media tarball, with offsite copy and a documented restore test.
5. **Observability:** add Sentry (or equivalent) for error tracking + release tagging; keep the existing JSON logging/rotating handlers and `/health` + admin health dashboard; add uptime monitoring against `/health`.

**Exit criteria:** `check --deploy` clean, HTTPS enforced, no secrets in VCS/compose, automated backup verified by a restore drill.

---

## Phase 2 — Core capability gaps (backend)

1. **Email / SMTP** — configure `EMAIL_BACKEND` (env-driven SMTP), `DEFAULT_FROM_EMAIL`. Wire transactional emails (RU templates under `delivery/templates/email/`): registration confirmation, password reset, key status transitions. Hook into the existing audit/transition flow in `delivery/services/orchestration.py`.
2. **Server-side push (FCM)** — add Firebase Admin SDK; new `delivery/services/push.py` sender + a device-token registration endpoint under `delivery/api/v1/`. Trigger pushes on order/parcel status transitions (same hook point as email). Store tokens per-user; handle token invalidation.
3. **Payment gateway** — integrate a provider (YooKassa recommended for RU; abstract behind `delivery/services/payments.py`):
   - Create payment on `AWAITING_PAYMENT`; expose intent/redirect via API for the mobile app.
   - Webhook endpoint → verify signature → transition order to `PAID` through `OrderStateService` (idempotent; reuse existing idempotency-key pattern).
   - Reconciliation view in admin + `FinancialTrace`/`Transaction` records.
   - **This is the phase that justifies adding Redis** (webhook/locking, throttle/cache correctness under load) — introduce it here if needed.

**Exit criteria:** end-to-end test order goes DRAFT→…→PAID via real gateway sandbox; user receives email + push on transitions.

---

## Phase 3 — Mobile release readiness (public stores)

Files: new `android/`, `ios/`, `lib/firebase_options.dart`, `pubspec.yaml`, CI config.

1. **Firebase project** — register Android + iOS apps; add `google-services.json`, `GoogleService-Info.plist`, generate `firebase_options.dart` (`flutterfire configure`). This makes the FCM code already in `lib/core/notifications/fcm_service.dart` actually function (paired with Phase 2 sender).
2. **Build flavors** — dev / staging / prod, each with its own `--dart-define=API_BASE_URL` and bundle/app id; document in `mobile-ds/CLAUDE.md` (already has the dart-define convention).
3. **Signing** — Android keystore + `key.properties` (gitignored); iOS certificates/provisioning profiles. Release build configs.
4. **Store assets** — app icons, splash screens, screenshots, store listings (RU), **privacy policy + Play Data Safety / Apple privacy nutrition labels** (the app stores emails, addresses, tracking — required).
5. **Crash reporting** — Firebase Crashlytics or Sentry.
6. **Mobile CI/CD** — pipeline: `flutter analyze` + `flutter test` → signed build → upload to Play internal track / TestFlight.
7. **Payment UX** — wire the Phase 2 payment intent into the order detail/checkout flow.

**Exit criteria:** signed release builds upload to both stores' internal/test tracks; push + payments work on a real device against staging.

---

## Phase 4 — PostgreSQL migration (GATED: only after Phases 0–3 problems fixed and all tests green)

Files: `config/settings.py` (DB block `settings.py:457-462`), `docker-compose.yml`, `requirements.txt`.

1. Add `postgres` service to compose; add `psycopg[binary]`; switch `DATABASES` to env-driven (`DATABASE_URL`).
2. Migrate data: `dumpdata` → `loaddata` (or `pgloader`) on a copy; verify row counts and FK integrity.
3. Audit for SQLite-isms (per CLAUDE.md constraint); run **full** suite against Postgres and fix divergences.
4. (Optional) move cache `DatabaseCache`→Redis and validate multi-worker Gunicorn (the original H-2 concern).
5. Re-point backups to `pg_dump`; rehearse restore.

**Exit criteria:** full suite green on Postgres under multi-worker Gunicorn; backup/restore verified.

---

## Phase 5 — Launch & ops

- Staging environment mirroring prod; automated smoke test post-deploy (extend the existing `deploy.yml` "verify mock data" step into real smoke checks, mock data disabled).
- Runbook: deploy, rollback, restore, incident response, cron jobs (`sync_fx_rates`, `check_order_consistency`, backups).
- Load/perf test of `/api/v1/orders` and auth under expected concurrency.
- Legal: privacy policy, ToS, data-retention/GDPR pages live (landing already has FAQ/footer content models).

---

## Verification strategy

- **Backend config:** `python manage.py check --deploy` (zero warnings with `DEBUG=False`); curl HTTPS redirect + security headers.
- **Backend tests:** `DJANGO_SETTINGS_MODULE=config.test_settings python manage.py test` green at every phase; add tests for email/push/payment services and webhook idempotency.
- **Payments:** sandbox end-to-end + webhook replay (idempotency) test.
- **Mobile:** `flutter analyze`, `flutter test`, signed `flutter build apk/ipa` per flavor; manual device run against staging for login → order → pay → push.
- **Infra:** backup→restore drill; staging smoke test on each deploy.

---

## Suggested execution order

`Phase 0 → Phase 1 → Phase 2 → Phase 3` (2 and 3 can overlap once Firebase exists) `→ Phase 4 (gated) → Phase 5`.

The first concrete action after approval: write this roadmap to `PLAN.md` at the projects root, then begin Phase 0 (mobile platform scaffolding is the single biggest blocker — the app is currently unbuildable).
