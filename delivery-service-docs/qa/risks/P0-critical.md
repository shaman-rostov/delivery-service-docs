# P0 — Critical (Release Blockers)

## Definition
Failures in these areas cause:
- Direct financial loss
- Broken core business flow
- Data corruption or inconsistency
- System unusable for customers

---

## 1. Authentication & Access Control

- User registration (email/social)
- Login / logout
- Password reset
- Session handling
- Account verification flow

**Risk:** Unauthorized access / inability to use platform

---

## 2. Buyout Order Core Flow

- Create order from product URL
- Multi-item order creation
- Order submission
- Order persistence in system
- Order status initialization

**Risk:** Core revenue stream breaks

---

## 3. Payment Processing

- Order payment success flow
- Payment failure handling
- Duplicate payment prevention
- Payment retry logic
- Partial payment handling
- Payment confirmation consistency

**Risk:** Direct financial loss / double charging

---

## 4. Currency Conversion & Pricing Engine

- Currency conversion accuracy
- Total price calculation
- Service fee calculation
- Order total consistency
- Rounding correctness

**Risk:** Incorrect billing / financial discrepancies

---

## 5. Warehouse Intake (Core Logic)

- Package registration
- Linking package to correct user
- Duplicate tracking prevention
- Package status correctness ("Received")

**Risk:** Lost or misassigned customer goods

---

## 6. Shipment Creation

- Create shipment from warehouse packages
- Tracking number generation
- Shipment status initialization
- Package-to-shipment mapping

**Risk:** Shipment misrouting or loss

---

## 7. Order State Machine Integrity

- Valid status transitions only
- No invalid transitions (e.g. Delivered → Processing)
- Cross-system status consistency

**Risk:** System data corruption

---
