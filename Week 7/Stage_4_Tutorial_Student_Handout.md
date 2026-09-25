# Stage 4 Object-Oriented Design Decisions

## Activity 1 - Encapsulation Review

| Class        | Protected state / invariant                                                                                                                                                                     | Public operations                                              |
|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| Patient      | Name and contact details must never be empty (FR-01, FR-12). The list of past appointments cannot be replaced from outside (FR-09).                                                             | updateContactDetails(), appointmentHistory(), addAppointment() |
| Practitioner | Name and specialty must never be empty (FR-03), and so must the identifier added during implementation. The appointments booked with this practitioner cannot be replaced from outside (FR-04). | appointments(), schedule(), addAppointment()                   |
| Appointment  | Patient, practitioner and time must all be present (FR-04, FR-11). Status must be one of the four values and change only as US-04 allows (FR-07).                                               | cancel(), complete(), markNoShow(), reschedule()               |

## Activity 2 - Composition or Inheritance?

**Appointment and Patient: Association**

An appointment refers to a patient; it is not a kind of one. It is association because FR-09 keeps past appointments as history, so an appointment is not destroyed with the record that refers to it.

**Appointment and Practitioner: Association**

It is association because FR-03 creates a practitioner record on its own, before any booking exists, and that record stays once the appointment is completed or cancelled.

**Doctor and Practitioner (hypothetical): Inheritance**

This is inheritance because a doctor is a practitioner. Everything true of a practitioner is true of a doctor, so a doctor holds the same name, specialty and schedule, and can be used anywhere a practitioner is expected.

**Clinic and Appointment: Composition**

Clinic is not in the model, as the clinic runs from one site. If it existed it would not be a kind of appointment, It would be composition because its appointments would exist only within that clinic and would go with it.

## Activity 3 - Responsibility Allocation

**Who decides whether SCHEDULED can become CANCELLED?**

Appointment decides. The rule in US-04 depends on what the status is at that moment, and Appointment is the object that holds it (FR-07). If any other object decided, the status would have to be exposed so that object could read and change it, which removes the protection the rule depends on.

**Who validates a patient name?**

Patient validates it. The name belongs to Patient, so Patient is the only class that should decide whether a value is acceptable, both when the record is created (FR-01) and when details are updated (FR-12). Appointment checks only that a patient was supplied at all (FR-11).

**Should Appointment execute SQL? Why?**

No, it should not. Appointment holds one booking and the rules about it, and SQL belongs to storage rather than to those rules. NFR-01 says data must survive a restart but does not say how, so the storage choice is still open and does not belong inside a domain class.

**Should the UI decide whether a status transition is legal?**

No, it should not. The rule has to hold for every caller, and a rule kept in the interface only holds for callers that go through it. NFR-03 says the same thing about bookings: a conflict must be blocked in the data, not only warned about on screen.

## Activity 4 - AI Code Critique

| # | Design problem                                                                                   | Correction                                                                                                                   |
|---|--------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| 1 | Status is public, so any caller can set it and bypass the rule in US-04.                         | Make status readable but not writable. Expose cancel(), complete() and markNoShow(), each checking the current status first. |
| 2 | Because status is a plain public attribute, it accepts any value, not only the four FR-07 names. | Use an AppointmentStatus enum so only those four values exist.                                                               |
| 3 | SQL inside cancel() mixes storage with a business rule, and NFR-01 leaves storage undecided.     | Remove it and cancel() changes the status and nothing else.                                                                  |
| 4 | A NotificationManager dependency, when reminders are provisional and unconfirmed.                | Remove it and leave reminders on the provisional list.                                                                       |
| 5 | Inheritance from PatientRecord, when an appointment is not a kind of patient record.             | Hold a reference to a Patient object instead.                                                                                |

## Exit question

**Why can code be object-oriented syntactically but still have poor object-oriented design?**

Classes, methods and inheritance are syntax. Design decides where responsibility sits and what each object is able to protect. The generated Appointment above passes the syntax test: it is a class, it has methods, it even uses inheritance. It still fails as a design. Its status can be set to anything by anyone, so a rule the client asked for is never enforced. It reaches into storage, so a question nobody has answered is built into the domain. It inherits from something it is not a kind of, so patient details are duplicated into every booking. Syntax gives you objects; design decides whether those objects can defend their own rules.
