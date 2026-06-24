# Risk-Based Test Prioritization (P0 / P1 / P2)
## Europe Shopping, Buyout & Package Forwarding Service

This document defines test execution priority based on business impact, financial risk, and system criticality.

---

# P0 — Critical (Release Blockers)

See [P0-critical.md](P0-critical.md) for detailed test cases.

## Definition
Failures in these areas cause:
- Direct financial loss
- Broken core business flow
- Data corruption or inconsistency
- System unusable for customers

---

## 1. [Authentication & Access Control](P0-critical.md#1-authentication--access-control)
## 2. [Buyout Order Core Flow](P0-critical.md#2-buyout-order-core-flow)
## 3. [Payment Processing](P0-critical.md#3-payment-processing)
## 4. [Currency Conversion & Pricing Engine](P0-critical.md#4-currency-conversion--pricing-engine)
## 5. [Warehouse Intake (Core Logic)](P0-critical.md#5-warehouse-intake-core-logic)
## 6. [Shipment Creation](P0-critical.md#6-shipment-creation)
## 7. [Order State Machine Integrity](P0-critical.md#7-order-state-machine-integrity)

---

# P1 — High Priority (Major Features)

See [P1-high-priority.md](P1-high-priority.md) for detailed test cases.

## Definition
Failures here:
- Do not block core flow completely
- Reduce usability or business efficiency
- Cause partial feature degradation

---

## 1. [Package Consolidation](P1-high-priority.md#1-package-consolidation)
## 2. [Shipping Cost Calculation](P1-high-priority.md#2-shipping-cost-calculation)
## 3. [Tracking & Delivery Updates](P1-high-priority.md#3-tracking--delivery-updates)
## 4. [Admin Order Management](P1-high-priority.md#4-admin-order-management)
## 5. [Promo Codes & Discounts](P1-high-priority.md#5-promo-codes--discounts)
## 6. [Notifications System](P1-high-priority.md#6-notifications-system)
## 7. [Profile & Address Management](P1-high-priority.md#7-profile--address-management)

---

# P2 — Low Priority (Nice to Have / UX / Edge)

See [P2-low-priority.md](P2-low-priority.md) for detailed test cases.

## Definition
Failures here:
- Do not affect core business operations
- Affect UX, polish, or edge cases
- Can be deferred post-release

---

## 1. [UI/UX Consistency](P2-low-priority.md#1-uiux-consistency)
## 2. [Content & Localization](P2-low-priority.md#2-content--localization)
## 3. [Performance (Non-Critical)](P2-low-priority.md#3-performance-non-critical)
## 4. [Audit Logs & Analytics](P2-low-priority.md#4-audit-logs--analytics)
## 5. [Support System Enhancements](P2-low-priority.md#5-support-system-enhancements)

---

# Priority Summary

| Priority | Impact Level | Business Risk | Release Blocking |
|----------|--------------|---------------|------------------|
| P0 | Critical | Financial / Core system failure | YES |
| P1 | High | Feature degradation | Conditional |
| P2 | Low | UX / cosmetic issues | NO |

---

# Execution Strategy

## Phase 1 — P0 Smoke Suite
Executed before every release:
- Authentication
- Buyout order creation
- Payment flow
- Warehouse intake
- Shipment creation
- Currency calculation

## Phase 2 — P1 Regression Suite
Executed before major releases:
- Consolidation
- Shipping logic
- Admin operations
- Notifications

## Phase 3 — P2 Optional Suite
Executed periodically:
- UI checks
- Performance sanity
- Localization review
