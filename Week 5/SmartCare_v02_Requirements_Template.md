# SmartCare v0.2 Requirements
### Completed Template

## 1. Problem and Scope

SmartCare Community Clinic currently uses spreadsheets and paper records to
manage patients, practitioners, and appointments. Staff report duplicate
bookings, difficulty finding patient information, inconsistent appointment
status, and limited appointment history. Management wants a small,
maintainable system covering patients, practitioners, and appointments.

### In scope

- Creating and searching patient records
- Creating practitioner records
- Booking, rescheduling, and cancelling appointments
- Preventing double-booking of a practitioner
- Tracking appointment status (booked / completed / cancelled / no-show)
- Per-patient appointment history
- Practitioner's own schedule view

### Out of scope (v1)

- Billing / insurance processing
- Clinical notes, diagnosis, or treatment-plan features
- Telehealth / video consultation
- Integration with external hospital or government health systems
- Patient-facing self-service app

### Provisional (needs client confirmation)

- Online patient self-service booking
- Automated SMS/email reminders
- Role-based access levels beyond a single "staff" role

## 2. Stakeholders

| Stakeholder | Need | Evidence |
|---|---|---|
| Receptionists / admin staff | Book, find, and update appointments quickly without duplicate or conflicting entries. | Brief states staff report duplicate bookings and difficulty finding patient information. |
| Practitioners | Accurate, up-to-date view of their own schedule and which patients they're seeing. | Brief states appointment status is inconsistent, implying practitioners can't rely on the current record. |
| Patients | Appointments booked correctly, with a reliable record of past visits. | Brief cites limited appointment history as a current problem affecting continuity of care. |
| Clinic management | Visibility into appointment activity and a system that's maintainable long-term. | Brief states management wants a "small, maintainable" system. |

## 3. Functional Requirements

- **FR-01:** The system shall allow a receptionist to create a new patient record with name and contact details.
- **FR-02:** The system shall allow a receptionist to search for a patient by name.
- **FR-03:** The system shall allow a receptionist to create a new practitioner record with name and specialty.
- **FR-04:** The system shall allow a receptionist to book an appointment for a patient with a specific practitioner at a given date and time.
- **FR-05:** The system shall prevent a new appointment being booked for a practitioner at a time that conflicts with an existing appointment for that practitioner.
- **FR-06:** The system shall allow a receptionist to view a list of all appointments for a given day.
- **FR-07:** The system shall allow a receptionist to update an appointment's status (booked, completed, cancelled, no-show), including cancelling an appointment.
- **FR-08:** The system shall allow a receptionist to reschedule an existing appointment to a new date and time.
- **FR-09:** The system shall maintain a history of past appointments for each patient, viewable by staff.
- **FR-10:** The system shall allow a practitioner to view their own schedule of upcoming appointments.
- **FR-11:** The system shall reject an appointment booking request that is missing a patient name, practitioner name, or appointment time.
- **FR-12:** The system shall allow a receptionist to update an existing patient's contact details. *(Added after AI review — see Section 8.)*

## 4. Non-Functional Requirements

- **NFR-01:** Recorded patient, practitioner, and appointment data shall not be lost if the application is restarted (data must persist between sessions).
- **NFR-02:** A receptionist with no prior training on the system shall be able to complete a standard appointment booking in under five steps.
- **NFR-03:** The system shall prevent duplicate or conflicting bookings at the data layer, not only through interface warnings, so invalid states cannot be saved.
- **NFR-04:** Code shall be organised into clearly named, documented functions so new features can be added without rewriting existing logic.
- **NFR-05:** Each core function (booking, cancelling, rescheduling, searching) shall be independently testable, with both normal and invalid inputs covered by tests.
- **NFR-06:** Access to patient data shall be restricted to authorised clinic staff, consistent with the clinic's (to-be-confirmed) privacy obligations.

## 5. User Stories

- **US-01:** As a receptionist, I want to book an appointment for a patient, so that they can see a practitioner at an agreed time.
- **US-02:** As a receptionist, I want to search for a patient by name, so that I can quickly find their appointment history.
- **US-03:** As a practitioner, I want to view my schedule for the day, so that I know which patients I am seeing and when.
- **US-04:** As a receptionist, I want to cancel an appointment, so that the freed time slot can be booked by another patient.
- **US-05:** As clinic management, I want to see a list of all appointments and their status, so that I can monitor daily clinic activity.
- **US-06:** As a receptionist, I want to update an existing patient's contact details, so that records stay accurate over time.

## 6. Acceptance Criteria

**US-01 — normal case**
GIVEN a patient and practitioner exist and the requested time is free
WHEN the receptionist submits a booking request with all required details
THEN the appointment is created and appears in the practitioner's schedule

**US-01 — negative / failure case**
GIVEN a practitioner already has an appointment at the requested time
WHEN the receptionist attempts to book another appointment for that practitioner at the same time
THEN the system rejects the booking and shows an error naming the conflicting time and practitioner

**US-04 — normal case**
GIVEN an existing appointment with status "booked"
WHEN the receptionist cancels it
THEN its status changes to "cancelled" and the time slot becomes available for new bookings

## 7. Assumptions and Open Questions

- Exact set of appointment status values (booked/completed/cancelled/no-show) is assumed, not yet confirmed by the client.
- Whether patient contact-detail edits (FR-12) require an audit trail is unconfirmed.
- Data persistence mechanism (file vs. database) for NFR-01 is not yet decided — a design decision for a later stage, not a requirement in itself.
- Access-control model (single staff role vs. role-based access, NFR-06) is provisional pending client input.

## 8. AI Requirements Review Record

| AI suggestion | Evidence? | Decision | Reason | Verification |
|---|---|---|---|---|
| Define a fixed appointment status list | Yes | Accepted | Brief cites "inconsistent appointment status" as a current problem. | Re-read brief; added explicit status list to FR-07. |
| FR/NFR persistence gap (NFR-01 has no matching FR) | Yes | Accepted | An NFR references a capability no FR provides. | Compared FR list against NFR-01 side by side; logged as open question (Section 7) rather than inventing a storage design. |
| Vague "no match" search message in US-02 | Yes | Accepted | Original wording was untestable as written. | Re-read the acceptance criterion for testability; required the message to state no match was found. |
| Missing patient-edit requirement | Yes | Accepted | Brief implies patient records need to stay accurate ("difficulty finding patient information"). | Checked client brief wording directly; added FR-12 and US-06. |
| FR-05 / FR-11 conceptual overlap | Yes | Modified | Both concern invalid bookings but trigger on different conditions. | Re-read both requirements to confirm they are not redundant; no merge needed. |
| Add exportable management report | No | Rejected | Not mentioned anywhere in the client brief; AI itself flagged this as an assumption. | Checked brief for any reporting mention — none found. |
| Add multi-language support | No | Rejected | No evidence in the brief; flagged by the AI as speculative. | Checked brief — no evidence; would be scope creep. |
| Usability step-targets for search/cancel (beyond NFR-02) | No (question, not a defect) | Unverified | Reasonable question, but needs real reception-staff input, not a one-person judgement call. | Logged as an open client question (Section 7) rather than resolved unilaterally. |
