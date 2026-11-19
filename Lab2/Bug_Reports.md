# Bug-log_Project – Bug Reports

**Student:** Srijan Kumar | **SRN:** PES1UG24CS467  
**Problem Statement #36:** Restaurant Table Booking & Pre-Ordering App  
**Jira Project:** Bug-log_Project (Bug tracking template)

| Bug | Summary | Linked Req. | Severity | Priority | Final Status |
|---|---|---|---|---|---|
| BUG-1 | Same table can be booked twice for the same time slot | FR-001 | Critical | Highest | Done |
| BUG-2 | Reserved table still shown as available on floor plan | FR-002 / NFR-001 | Major | High | Done |
| BUG-3 | Party size accepts 0 and negative values | FR-003 | Minor | Medium | Done |
| BUG-4 | Preparation time ignores items added after first save | FR-004 | Major | Medium | In Progress |
| BUG-5 | Cancelled reservation does not release the table | FR-005 | Major | High | Done |
| BUG-6 | Full card number printed on booking receipt | NFR-002 | Critical | Highest | To Do |

---

## BUG-1: Same table can be booked twice for the same time slot
- **Environment:** Web app, Chrome 131, macOS
- **Steps to reproduce:**
  1. Diner A opens the floor plan and selects Table 4 for 8:00 PM.
  2. Diner B selects Table 4 for 8:00 PM at the same time.
  3. Both click "Confirm Reservation".
- **Expected:** Second confirmation is rejected with "Table no longer available".
- **Actual:** Both reservations are confirmed for Table 4 at 8:00 PM.
- **Fix:** Lock the table slot during confirmation; reject the second write.

## BUG-2: Reserved table still shown as available on floor plan
- **Environment:** Web app, Chrome 131
- **Steps to reproduce:**
  1. Reserve Table 2 for 7:30 PM.
  2. Open the floor plan in a second browser for 7:30 PM.
- **Expected:** Table 2 shows as Reserved within 2 seconds (NFR-001).
- **Actual:** Table 2 stays green (Available) until the page is refreshed.
- **Fix:** Push status updates to open clients instead of loading only on page load.

## BUG-3: Party size accepts 0 and negative values
- **Environment:** Web app, reservation form
- **Steps to reproduce:**
  1. Open the reservation form.
  2. Enter party size `0` (or `-2`) and submit.
- **Expected:** Validation error: "Party size must be between 1 and 20".
- **Actual:** Reservation is created with party size 0.
- **Fix:** Add min/max validation on the form and server side.

## BUG-4: Preparation time ignores items added after first save
- **Environment:** Web app, pre-order screen
- **Steps to reproduce:**
  1. Pre-order 1 starter and save. Note the preparation time (e.g. 10 min).
  2. Edit the order, add a main course (25 min), and save.
- **Expected:** Preparation timeline updates to include the main course.
- **Actual:** Preparation timeline stays at 10 min.
- **Fix:** Recalculate the timeline every time the order is modified.

## BUG-5: Cancelled reservation does not release the table
- **Environment:** Manager dashboard
- **Steps to reproduce:**
  1. Manager cancels the reservation for Table 6, 9:00 PM.
  2. A diner checks Table 6 for 9:00 PM.
- **Expected:** Table 6 is available again.
- **Actual:** Table 6 still shows as Reserved.
- **Fix:** On cancellation, update the table status and the availability cache.

## BUG-6: Full card number printed on booking receipt
- **Environment:** Booking confirmation receipt (PDF/email)
- **Steps to reproduce:**
  1. Complete a booking with a pre-order payment.
  2. Open the confirmation receipt.
- **Expected:** Only the last 4 digits are shown (e.g. `**** **** **** 4242`).
- **Actual:** The full 16-digit card number is printed.
- **Fix:** Mask payment details; never store or display the full card number.

## Workflow Used
`To Do → In Progress → Done`. Each bug was created with the Bug work type, then assigned, prioritized, linked to its requirement, commented on during the fix, and moved across the board.
