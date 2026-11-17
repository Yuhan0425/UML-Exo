# Design Questions

## 1. Patient consulting multiple doctors?
A `HealthProblem` is assigned to one doctor by default (`0..1`).
To support several doctors, replace this association with an association class
`Consultation(problem, doctor, status, date)`:
`HealthProblem 1 -- 0..* Consultation 0..* -- 1 Doctor`.
Each consultation can produce its own prescription.

## 2. Reusable prescriptions?
Yes. Add a self-association `Prescription.basedOn` (copy-on-reuse): a new prescription is
cloned from an old one, keeping history and timestamps intact. Alternatively, a
`PrescriptionTemplate` class owned by a doctor (Prototype pattern).

## 3. Which design pattern for payments?
**Strategy pattern**: a `PaymentMethod` interface with `process()` implemented by
`Check`, `Cash`, `CreditCard`. Add a **Factory** to create the right one and
**Observer** to notify organizer/doctor on payment events.

## 4. Data integrity for payments?
- Database transactions (ACID) around "record payment + update status"
- Idempotency keys to avoid double charging
- Status state machine: PENDING -> PAID -> FORWARDED (no skipping)
- Foreign keys / unique constraints (one payment per prescription)
- Audit log (who, when) and role-based access control
- Never store full card numbers (use a payment gateway / tokens)
