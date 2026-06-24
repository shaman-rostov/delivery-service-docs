# Test Scenarios
## Europe Shopping, Buyout & Package Forwarding Service

This document defines high-level QA scenarios derived from the business process mind map.

---

# 1. Customer Journey Scenarios

## 1.1 Registration & Authentication

- User successfully registers using email
- User successfully registers using social login (Google/Apple)
- System rejects invalid email format during registration
- System prevents registration with already existing email
- Email verification link expires and requires resend
- User logs in with valid credentials
- User fails login with incorrect password
- User resets password via email recovery flow
- User logs out and session is invalidated

---

## 1.2 Profile Setup & Settings

- User updates personal profile successfully
- User sets valid delivery address
- System blocks unsupported destination country
- User changes preferred currency and UI updates accordingly
- User changes language and interface updates correctly
- Profile changes persist after logout/login

---

## 1.3 Product Buyout Flow

- User creates buyout order using valid product URL
- User creates order with multiple product URLs
- System rejects invalid or unreachable product URL
- User manually adds product without URL
- System calculates estimated price correctly
- System recalculates price after quantity change
- User submits order successfully and receives confirmation
- Order appears in user dashboard with correct status

---

## 1.4 Order Management

- User views list of all orders
- User opens order details page
- User sees correct order status updates
- User cancels order before processing starts
- System blocks cancellation for processed orders
- Support messages are linked to specific order
- Order history remains consistent across sessions

---

## 1.5 Payment Flow

- User successfully pays for buyout order
- User successfully pays for shipping fee
- Payment fails due to insufficient funds
- Payment gateway timeout is handled safely
- User retries failed payment successfully
- System prevents duplicate payment submission
- Partial payment is recorded correctly
- Refund is processed and reflected in balance

---

## 1.6 Delivery & Tracking

- User tracks shipment using tracking number
- Tracking shows correct shipment status updates
- Shipment transitions from In Transit → Delivered
- System handles delayed or missing carrier updates
- User receives delivery confirmation notification

---

# 2. Warehouse Operations Scenarios

## 2.1 Incoming Packages

- Warehouse registers incoming package successfully
- Package is linked to correct customer account
- Duplicate tracking number is rejected
- Package status is updated to "Received"

---

## 2.2 Storage Management

- Package is assigned correct storage location
- System tracks storage duration correctly
- Multiple packages stored per customer
- Inventory reflects real-time updates

---

## 2.3 Package Processing

- Warehouse uploads package photos successfully
- System records weight and dimensions correctly
- Damaged package is marked in system
- Missing package data triggers validation error

---

## 2.4 Outgoing Shipments

- User selects multiple packages for shipment
- System creates shipment from selected packages
- Shipment label is generated successfully
- Packages are marked as shipped
- Carrier receives correct shipment data

---

# 3. Financial Scenarios

## 3.1 Pricing & Calculation

- System calculates product price correctly
- Service fee is applied correctly
- Currency conversion uses correct rate
- Total price matches breakdown sum
- Price recalculates after order modification

---

## 3.2 Payment Processing

- Successful payment completes order
- Failed payment keeps order in unpaid state
- Duplicate payment attempts are blocked
- Payment confirmation updates order status

---

## 3.3 Shipping Cost Calculation

- Shipping cost is calculated based on weight
- Shipping cost depends on destination country
- Carrier-specific pricing rules are applied
- Final invoice matches selected shipping method

---

## 3.4 Refunds

- Full refund is processed for cancelled order
- Partial refund is processed correctly
- Refund is rejected for completed delivery
- Refund is reflected in user balance

---

# 4. Admin Panel Scenarios

## 4.1 User Management

- Admin views list of users
- Admin blocks/unblocks user account
- Admin views user order history
- Admin communicates with customer

---

## 4.2 Order Management

- Admin approves buyout orders
- Admin modifies order price
- Admin changes order status workflow
- Admin cancels orders with reason

---

## 4.3 Warehouse Management

- Admin registers incoming packages
- Admin assigns storage location
- Admin updates package status
- Admin triggers consolidation process

---

## 4.4 Shipping Management

- Admin creates shipment from warehouse packages
- Admin generates tracking number
- Admin updates shipment status
- Carrier sync updates tracking data

---

## 4.5 Promotions

- Admin creates promo code
- System applies discount correctly
- Expired promo codes are rejected
- Referral bonus is applied correctly

---

# 5. Integration Scenarios

## 5.1 Payment Gateway Integration

- Payment gateway returns success response
- Payment gateway returns failure response
- Payment timeout is handled correctly
- Fraud detection blocks suspicious payments

---

## 5.2 Shipping Carrier Integration

- Shipment label is generated via API
- Tracking number is returned and stored
- Carrier updates shipment status correctly
- API failure triggers retry mechanism

---

## 5.3 Currency Exchange Integration

- System fetches live exchange rates
- Currency switch updates all prices
- Fallback rate is used when API fails

---

## 5.4 Notification System

- Email sent after order creation
- Email sent after payment confirmation
- SMS sent on shipment dispatch
- Notification failure does not break flow

---

# 6. Support System Scenarios

## 6.1 Customer Support

- User creates support ticket
- Ticket is linked to correct order
- Support responds to user request
- Ticket is closed after resolution

---

## 6.2 Internal Escalation

- Warehouse issue escalated to admin
- Payment issue escalated to finance team
- Technical issue escalated to engineering

---

## 6.3 Resolution Flow

- Issue is investigated
- Status updated during resolution
- User receives resolution notification
- Feedback collected after closure

---

# 7. High-Risk Scenarios

## 7.1 Payment Risks

- Prevent double charging
- Detect payment mismatch
- Prevent duplicate order creation on retry

---

## 7.2 Shipping Risks

- Prevent package loss between warehouse and carrier
- Prevent incorrect destination shipping
- Ensure tracking number consistency

---

## 7.3 Currency Risks

- Prevent incorrect currency conversion
- Ensure correct totals after currency switch

---

## 7.4 Order State Risks

- Prevent invalid status transitions
- Ensure consistent status across systems

---

## 7.5 Consolidation Risks

- Prevent merging packages from different users
- Prevent duplicate package inclusion
- Ensure correct recalculation after changes
