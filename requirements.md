# 📚 Complete Expected Features List

_Build a fitness app backend like Cult Fit._

---

## 1. User Management

### Feature:

Users can register as either a `user` or an `admin`.

### Details:

- Email must be unique.
- Phone number must follow E.164 format.
- User has a `strike_count`, `no_show_count`, and optional `banned_until` timestamp.
- Users can update name and phone (not email).
- Deleting a user must:
  - Cancel their future bookings.
  - Remove them from waitlists.
  - Trigger any waitlist promotions if needed.

### Corner Cases:

- Trying to create duplicate emails should fail.
- Deleting a user who has waitlisted multiple classes must promote correctly.

---

## 2. Authentication and Authorization

_(Not mandatory)_

### Feature:

Secure APIs using JWT and enforce access control based on roles (`user`, `admin`).

### Details:

- Normal users cannot access Admin APIs.
- Authorization failure must return a standard error.

### Corner Cases:

- Expired JWT tokens.
- Missing tokens.
- Role mismatch access (user hitting admin API).

---

## 3. Center Management

### Feature:

Admin can manage Gym Centers.

### Details:

- Create, Update, View, Delete Centers.
- A center cannot be deleted if active classes are scheduled.

### Center Holidays:

- Admin can declare blackout dates when the center will be closed.
- All class instances on holiday dates must be cancelled automatically.
- Users with bookings on cancelled dates must be notified by email.

### Corner Cases:

- Declaring overlapping holidays should merge dates cleanly.
- Trying to delete a center with live bookings should block the operation.

---

## 4. Class Management (Single and Recurring)

### Feature:

Classes can be created either as one-time events or recurring schedules.

### Details:

- **Single Class:**

  - Happening on one specific date.

- **Recurring Class:**

  - Happens on multiple days every week.
  - Recurring period has a start and end date.

- 50 minutes class duration mandatory.
- 10-minute mandatory buffer between any two classes.
- Center holiday blackout must block class creation on those days.

### Corner Cases:

- Overlapping class timings (even across different class types) must be rejected.
- Center holidays must cancel all corresponding class instances.

---

## 5. Class Instance Management

### Feature:

Each scheduled date of a recurring class becomes a **Class Instance**.

### Details:

- Bookings happen against instances.
- Center holidays must delete/cancel generated instances.
- Late cancellations update future instances dynamically.

### Corner Cases:

- Modifying recurrence of class should regenerate future instances safely.
- Cancelling a center must cancel all child class instances.

---

## 6. Booking Management and Waitlist

### Feature:

Users can book available spots in class instances.

### Details:

- Each class instance has a defined capacity.
- Bookings beyond capacity are added to a waitlist.
- Booking closes 1 hour before class start.
- Max 4 active bookings per user per week allowed.

### Corner Cases:

- Double-booking same class instance must fail.
- Overbooking must place users on waitlist and provide correct waitlist position.
- Cancelling a booking must promote first waitlisted user.

---

## 7. Attendance Management

### Feature:

Users must mark themselves as **present** within the first 5 minutes of class starting.

### Details:

- If attendance is not marked, it counts as a **no-show**.
- Attendance cannot be marked after 5 mins.

### Corner Cases:

- Trying to mark attendance after 5 mins should fail.
- If user does not mark attendance, no-show logic must trigger automatically.

---

## 8. Cancellation Management and Penalties

### Feature:

Late cancellations and no-shows result in strikes and temporary bans.

### Details:

- Cancelling within 2 hours of class start → 1 strike.
- No-show without attendance marking → 1 strike.
- 3 strikes → 3-day booking ban.
- 3 no-shows → 7-day booking ban.

### Corner Cases:

- System must automatically reset strike counters monthly.
- System must auto-unban users after penalty period expires.

---

## 9. Penalty Management APIs

### Feature:

Users and admins must be able to view penalty and strike information.

### Details:

- Strike Count
- No-Show Count
- Active Bans

---

## 10. Weekly Booking Limit Enforcement

### Feature:

A user can have at most **4 active bookings per week**.

### Details:

- System must count future active bookings dynamically.
- Older bookings expiring/cancelled must free up quota.

### Corner Cases:

- If cancellation happens, user should immediately be able to book a new class within quota.
- Attempt to exceed limit should fail cleanly.

---

## 11. Exercise Management

### Feature:

Admin can attach exercises (ordered) to each class.

### Details:

- Exercises have:
  - Name
  - Description
  - Difficulty Level
  - Video URL (optional)

### Corner Cases:

- Changing order of exercises should be atomic (transactionally reorder all).
- Duplicate orders must be disallowed.

---

## 12. Workout Logging (User Logs)

### Feature:

Users can log actual workout performance per class and per exercise.

### Details:

- Record:
  - Sets
  - Reps per set
  - Optional Comment

### Corner Cases:

- Cannot log for non-attended sessions.
- Cannot log exercises outside of scheduled class-exercise list.

---

## 13. Workout Goals and Progress Tracking

### Feature:

Users can set workout goals.

### Details:

- Goal
  - Exercise name
  - Target Sets and reps
- Systems track actual vs target over time

### Workout Goal Progress Retrieval:

- User must be able to view progress history against goal.

### Corner Cases:

- System must auto-mark goals as “Achieved” when targets crossed.
- User can delete/redefine goals.

---

## 14. No-Show Management

### Feature:

Users missing attendance must accumulate no-show counts.

### Details:

- After 3 no-shows, ban for 7 days.

### No-show History Retrieval:

- User should be able to see list of no-show class instances.

### Corner Cases:

- Class marked as no-show if class is cancelled later should clean up no-show mark.

---

## 15. Admin Reporting Features

### Feature:

Admins can view operational metrics.

### Details:

- Center Utilization Report (daily):
  - Classes held
  - Occupancy percentages
- Active Holidays list.

### Corner Cases:

- Should handle days with no classes separately.
- Past reports should not be modified if center holidays are declared after.

---
