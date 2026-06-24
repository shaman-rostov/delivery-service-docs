# QA Checklists
## Europe Shopping, Buyout & Package Forwarding Service

This document contains execution checklists for Smoke, Regression, Release, and Security validation.

---

# 1. Smoke Checklist (P0 — Must Pass Before Every Release)

## 1.1 Authentication

- [ ] User can register successfully (email)
- [ ] User can register successfully (social login)
- [ ] User can login with valid credentials
- [ ] Invalid login is rejected
- [ ] Password reset flow works
- [ ] User session persists correctly
- [ ] User can logout successfully

---

## 1.2 Core Buyout Flow

- [ ] User can create buyout order
- [ ] Product URL is accepted and validated
- [ ] Order is created with correct status (New)
- [ ] Order appears in dashboard
- [ ] Order details are accessible

---

## 1.3 Payment Flow

- [ ] User can successfully pay for order
- [ ] Payment failure is handled correctly
- [ ] Duplicate payment is blocked
- [ ] Payment confirmation updates order status
- [ ] Receipt is generated

---

## 1.4 Warehouse Intake

- [ ] Package can be registered in system
- [ ] Package is linked to correct user
- [ ] Duplicate tracking number is rejected
- [ ] Package status is set to "Received"

---

## 1.5 Shipment Creation

- [ ] Shipment can be created from warehouse packages
- [ ] Tracking number is generated
- [ ] Shipment status is set to "Dispatched"
- [ ] Shipment is visible to user

---

## 1.6 Currency & Pricing

- [ ] Order total is calculated correctly
- [ ] Currency conversion is applied correctly
- [ ] No mismatch between breakdown and total

---

# 2. Regression Checklist (P1 — Full System Stability)

## 2.1 Order Management

- [ ] User can view all orders
- [ ] Order status updates correctly
- [ ] Order cancellation works
- [ ] Order history remains consistent
- [ ] Support messages linked to orders

---

## 2.2 Buyout Flow Extended

- [ ] Multiple product URLs in one order
- [ ] Manual product creation works
- [ ] Quantity updates recalculate price
- [ ] Invalid URLs are rejected

---

## 2.3 Payment Extended

- [ ] Partial payments are supported
- [ ] Payment retry works correctly
- [ ] Refunds are processed correctly
- [ ] Payment timeout handled safely

---

## 2.4 Warehouse Operations

- [ ] Package photos upload successfully
- [ ] Weight and dimensions stored correctly
- [ ] Damaged packages are flagged
- [ ] Inventory updates in real-time

---

## 2.5 Package Consolidation

- [ ] Packages can be merged
- [ ] Packages can be removed from consolidation
- [ ] Consolidation cost is recalculated
- [ ] Cross-user consolidation is blocked

---

## 2.6 Shipping & Tracking

- [ ] Shipping cost calculation is correct
- [ ] Carrier integration works
- [ ] Tracking updates are received
- [ ] Delivery status updates correctly

---

## 2.7 Admin Panel

- [ ] Admin can manage users
- [ ] Admin can update order status
- [ ] Admin can register warehouse packages
- [ ] Admin can create shipments
- [ ] Admin can manage promo codes

---

## 2.8 Notifications

- [ ] Email sent on order creation
- [ ] Email sent on payment success
- [ ] Email sent on shipment dispatch
- [ ] Notifications do not break flow if failed

---

# 3. Release Checklist (Go/No-Go Validation)

## 3.1 Business Readiness

- [ ] All P0 scenarios passed
- [ ] No Critical open bugs
- [ ] No payment issues detected
- [ ] No shipment blocking issues
- [ ] Currency conversion verified

---

## 3.2 Data Integrity

- [ ] No order duplication issues
- [ ] No payment duplication issues
- [ ] Order state transitions valid
- [ ] Warehouse data consistent
- [ ] Shipment data consistent

---

## 3.3 System Stability

- [ ] Core API endpoints respond correctly
- [ ] No critical server errors in logs
- [ ] No timeout issues in payment flow
- [ ] No integration failures (payment/shipping)

---

## 3.4 User Experience

- [ ] Core flows are usable end-to-end
- [ ] No blocking UI issues
- [ ] Mobile responsiveness acceptable
- [ ] Key pages load correctly

---

# 4. Security Checklist

## 4.1 Authentication & Authorization

- [ ] Unauthorized access is blocked
- [ ] Users cannot access other users' orders
- [ ] Admin endpoints are protected
- [ ] Session tokens expire correctly

---

## 4.2 Payment Security

- [ ] Payment data is not exposed in UI
- [ ] No duplicate charge vulnerability
- [ ] Webhook validation is implemented
- [ ] Payment callbacks are verified

---

## 4.3 Input Validation

- [ ] Invalid product URLs are rejected
- [ ] SQL injection attempts blocked
- [ ] XSS inputs are sanitized
- [ ] File upload validation exists

---

## 4.4 Data Protection

- [ ] Sensitive user data is masked
- [ ] Logs do not contain payment details
- [ ] Personal data is securely stored
- [ ] API responses do not leak internal data

---

# 5. Performance Checklist (Basic Validation)

- [ ] Login page loads within acceptable time
- [ ] Dashboard loads without delays
- [ ] Order creation completes quickly
- [ ] Payment flow does not timeout
- [ ] Warehouse operations are responsive
- [ ] API responses are under acceptable threshold

---

# 6. Integration Checklist

## 6.1 Payment Provider

- [ ] Successful payment callback received
- [ ] Failed payment handled correctly
- [ ] Timeout scenario handled safely

---

## 6.2 Shipping Carrier

- [ ] Tracking number generated successfully
- [ ] Status updates received correctly
- [ ] Label generation works

---

## 6.3 Currency Service

- [ ] Exchange rates fetched successfully
- [ ] Fallback rates applied when API fails
- [ ] Currency switching updates totals

---

## 6.4 Notification Service

- [ ] Email service sends messages
- [ ] SMS (if used) sends messages
- [ ] Failures do not block main flow
