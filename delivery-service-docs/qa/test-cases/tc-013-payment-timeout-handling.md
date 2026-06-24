## TC-013: Payment Timeout Handling

**Preconditions:**
- Order is ready for payment

### Steps:
1. Start payment process
2. Simulate gateway timeout

### Expected Results:
- System shows "Payment pending" or "failed"
- Order status remains consistent
- No duplicate charge occurs
