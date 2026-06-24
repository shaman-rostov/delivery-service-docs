## TC-026: Order State Consistency

**Preconditions:**
- System is running and operational

### Steps:
1. Create order
2. Move through all statuses
3. Refresh system multiple times

### Expected Results:
- Status remains consistent across UI and backend
- No invalid transitions occur
