# SmartCare Requirements Specification 
### Stage 2 Lab — AI OFF → AI ON → VERIFY

This document consolidates the full Stage 2 deliverable: the requirements
specification (Parts A–E, H), the AI review and verification (Parts F–G),
and the reflection.

---

## Part A – Client Brief (context)

SmartCare uses spreadsheets and paper records. Staff report duplicate
bookings, difficulty finding patient information, inconsistent appointment
status, and limited appointment history. Management wants a small,
maintainable patient, practitioner, and appointment system.

### The clinic is experiencing:

- Duplicate appointment bookings.
- Difficulty locating patient information quickly.
- Inconsistent appointment status information.
- Limited access to reliable appointment history.

## Part B – Stakeholders and Scope

### Stakeholders

| Stakeholder | Interest / need |
|---|---|
| Receptionists / admin staff | Book, find, and update appointments quickly without duplicate or conflicting entries. |
| Practitioners | Accurate, up-to-date view of their own schedule and which patients they're seeing. |
| Patients | Appointments booked correctly, with a reliable record of past visits. |
| Clinic management | Visibility into appointment activity and confidence the system is maintainable long-term. |

### In scope

- Creating and searching patient records
- Creating practitioner records
- Booking, rescheduling, and cancelling appointments
- Preventing double-booking of a practitioner
- Tracking appointment status (booked / completed / cancelled / no-show)
- Per-patient appointment history
- Practitioner's own schedule view

### Out of scope 

- Billing / insurance processing
- Clinical notes, diagnosis, or treatment-plan features
- Telehealth / video consultation
- Integration with external hospital or government health systems
- Patient-facing self-service app

### Provisional (needs client confirmation)

- Online patient self-service booking
- Automated SMS/email reminders
- Role-based access levels beyond a single "staff" role

## Part C – Functional Requirements

| ID | Requirement |
|---|---|
| FR-01 | The system shall allow a receptionist to create a new patient record with name and contact details. |
| FR-02 | The system shall allow a receptionist to search for a patient by name. |
| FR-03 | The system shall allow a receptionist to create a new practitioner record with name and specialty. |
| FR-04 | The system shall allow a receptionist to book an appointment for a patient with a specific practitioner at a given date and time. |
| FR-05 | The system shall prevent a new appointment being booked for a practitioner at a time that conflicts with an existing appointment for that practitioner. |
| FR-06 | The system shall allow a receptionist to view a list of all appointments for a given day. |
| FR-07 | The system shall allow a receptionist to update an appointment's status (booked, completed, cancelled, no-show), including cancelling an appointment. |
| FR-08 | The system shall allow a receptionist to reschedule an existing appointment to a new date and time. |
| FR-09 | The system shall maintain a history of past appointments for each patient, viewable by staff. |
| FR-10 | The system shall allow a practitioner to view their own schedule of upcoming appointments. |
| FR-11 | The system shall reject an appointment booking request that is missing a patient name, practitioner name, or appointment time. |
| FR-12 | The system shall allow a receptionist to update an existing patient's contact details. *(added after AI review — see "AI Requirements Review and Verification" below)* |

## Part D – Non-Functional Requirements

| ID | Requirement |
|---|---|
| NFR-01 | Recorded patient, practitioner, and appointment data shall not be lost if the application is restarted (data must persist between sessions). |
| NFR-02 | A receptionist with no prior training on the system shall be able to complete a standard appointment booking in under five steps. |
| NFR-03 | The system shall prevent duplicate or conflicting bookings at the data layer, not only through interface warnings, so invalid states cannot be saved. |
| NFR-04 | Code shall be organised into clearly named, documented functions so new features can be added without rewriting existing logic. |
| NFR-05 | Each core function (booking, cancelling, rescheduling, searching) shall be independently testable, with both normal and invalid inputs covered by tests. |
| NFR-06 | Access to patient data shall be restricted to authorised clinic staff, consistent with the clinic's (to-be-confirmed) privacy obligations. |

## Part E – User Stories and Acceptance Criteria

**US-01:** As a receptionist, I want to book an appointment for a patient so
that they can see a practitioner at an agreed time.

- **Given** a patient and practitioner exist and the requested time is free
  **When** the receptionist submits a booking request with all required details
  **Then** the appointment is created and appears in the practitioner's schedule
- *Negative:* **Given** a practitioner already has an appointment at the
  requested time
  **When** the receptionist attempts to book another appointment for that
  practitioner at the same time
  **Then** the system rejects the booking and shows an error naming the
  conflicting time and practitioner

**US-02:** As a receptionist, I want to search for a patient by name so that
I can quickly find their appointment history.

- **Given** at least one patient record matches the search text
  **When** the receptionist searches using part or all of a patient's name
  **Then** matching patient records are returned
- *Negative:* **Given** no patient record matches the search text
  **When** the receptionist searches
  **Then** the system clearly states no matching patient was found, rather
  than showing an empty or misleading list

**US-03:** As a practitioner, I want to view my schedule for the day so that
I know which patients I am seeing and when.

**US-04:** As a receptionist, I want to cancel an appointment so that the
freed time slot can be booked by another patient.

- **Given** an existing appointment with status "booked"
  **When** the receptionist cancels it
  **Then** its status changes to "cancelled" and the time slot becomes
  available for new bookings
- *Negative:* **Given** an appointment already marked "completed"
  **When** the receptionist attempts to cancel it
  **Then** the system prevents the cancellation and explains that a
  completed appointment cannot be cancelled

**US-05:** As clinic management, I want to see a list of all appointments
and their status so that I can monitor daily clinic activity.

### Part F – AI Requirements Review (AI ON)

**Prompt used** (UC-approved GenAI tool):

> Act as a software requirements reviewer. Review the SmartCare
> requirements for ambiguity, inconsistency, missing clarification
> questions, and testability. Do NOT invent new client requirements. For
> every suggestion, state whether it is based on evidence or is only a
> question/assumption requiring validation.


**AI reviewer's suggestions (summarised):**

| # | Suggestion | Evidence-based or assumption? |
|---|---|---|
| 1 | FR-05 (conflict prevention) and the "missing field" requirement overlap conceptually — consider clarifying that they test different failure modes. | Evidence-based — both requirements are about rejecting an invalid booking. |
| 2 | The appointment status values (booked/completed/cancelled/no-show) are used in FR-07 and the acceptance criteria but never explicitly defined as a fixed list. | Evidence-based — the values are used before being defined anywhere in the spec. |
| 3 | NFR-01 requires data to persist between sessions, but no functional requirement says how or where data is stored. | Evidence-based — an NFR references a capability no FR provides. |
| 4 | US-02's negative scenario says the system should "clearly state" no match was found, but doesn't specify what that message should say — hard to test as written. | Evidence-based — acceptance criteria should be specific enough to verify. |
| 5 | There is no requirement covering editing an existing patient's contact details, even though the client brief mentions "difficulty finding patient information," which implies records need to stay accurate over time. | Evidence-based — inferred from the client brief, not invented. |
| 6 | Consider adding an exportable management report feature. | **Assumption / invention** — not mentioned anywhere in the client brief; the AI itself flagged this as something to validate with the client, not a confirmed need. |
| 7 | Consider adding multi-language support for the interface. | **Assumption / invention** — no evidence in the client brief; flagged by the AI as speculative. |
| 8 | NFR-02 ("under five steps") is measurable, but there is no equivalent measurable target for search or cancellation — reviewer asked whether similar usability targets are wanted for those actions. | Question requiring validation — not evidence of a defect, just a prompt to consider scope consistency. |

### Part G – Verify the AI Review

| # | AI suggestion | Decision | Evidence used to decide |
|---|---|---|---|
| 1 | FR-05 / missing-field overlap | **Modified** | Re-read both requirements: they trigger on different conditions (a time conflict vs. an absent field). Kept both as separate FRs (FR-05, FR-11) but confirmed neither is redundant — no change needed beyond re-checking wording. |
| 2 | Undefined status values | **Accepted** | Client brief explicitly lists "inconsistent appointment status" as a current problem — the fix (defining a fixed status list in FR-07) directly addresses stated evidence, not just tidiness. |
| 3 | FR/NFR persistence inconsistency | **Accepted** | Confirmed by re-reading NFR-01 and the FR list side by side — the gap is real. Rather than inventing a storage mechanism (a design decision, out of scope for a requirements doc), it was logged as an open question in Part H above. |
| 4 | Vague "no match" message | **Accepted** | Re-read US-02's original negative scenario — it was genuinely untestable as written ("clearly states" is vague). Rewording still avoids prescribing exact UI text (a design decision), but requires the message to name that no match was found. |
| 5 | Missing patient-edit requirement | **Accepted** | Checked the client brief directly: "difficulty finding patient information" implies stale/incorrect records are part of the problem, which an edit capability addresses. Added as FR-12. |
| 6 | Exportable management report | **Rejected** | No mention in the client brief of reporting needs; the AI itself labelled this an assumption. Not added — would be scope creep without client confirmation. |
| 7 | Multi-language support | **Rejected** | Same reasoning as #6 — no evidence in the brief, and the AI flagged it as speculative rather than evidence-based. |
| 8 | Usability targets for other actions | **Unverified** | Reasonable question, but deciding specific step-count targets for every action needs input from actual reception staff, not a one-person judgement call. Logged as a question for the client rather than accepted or rejected outright. |

**Pattern in the review:** every suggestion the AI itself labelled
"evidence-based" traced back to a sentence in the original client brief.
The two suggestions it could not tie to the brief (#6, #7) were exactly the
two rejected here — confirming that flagging evidence vs. assumption during
the AI request made the verify step faster and less subjective.
-e 
---

## Part H – SmartCare v0.2

This document contains: stakeholder analysis,
in/out-of-scope and provisional features, 12 functional requirements
(FR-01–FR-12), 6 non-functional requirements (NFR-01–NFR-06), and 5 user
stories with Given-When-Then acceptance criteria for three, each including
one negative scenario.

Assumptions and open questions carried forward from this stage:

1. Exact set of appointment status values (booked/completed/cancelled/no-show)
   is assumed, not yet confirmed by the client.
2. Whether patient contact-detail edits (FR-12) require an audit trail is
   unconfirmed.
3. Data persistence mechanism (file vs. database) for NFR-01 is not yet
   decided — this is a design decision for a later stage, not a requirement
   in itself.
4. Access-control model (single staff role vs. role-based access, NFR-06) is
   provisional pending client input.

Selected AI review evidence supporting these requirements is documented in
the "AI Requirements Review and Verification" section.
---



## Reflection

Before AI, I drafted stakeholders, scope, functional and non-functional
requirements, and user stories directly from the client brief. Writing
FR-07 forced me to notice that "inconsistent appointment status" in the
brief meant I needed a defined, fixed set of status values, not just a
vague "status" field.

AI noticed things I missed: that NFR-01 (data must persist) had no matching
functional requirement explaining how, and that my negative acceptance
criterion for patient search ("clearly states no match") wasn't specific
enough to actually test. Both were genuine gaps, and both were fixed
without needing to invent new client needs.

AI did overreach twice, suggesting an exportable management report and
multi-language support. Neither appears anywhere in the client brief, and
the AI review itself flagged both as assumptions rather than evidence, which
made them easy to reject with confidence rather than guesswork.

The requirement that changed most was patient records: FR-12 (editing
contact details) didn't exist in my first draft, but was clearly implied by
the brief's complaint about "difficulty finding patient information."

Requirements need evidence because a requirements document is a contract
about what will be built and judged against later. If I accept a suggestion
just because it sounds professional, I'm no longer describing the client's
actual problem, I'm inventing scope the client never asked for and may not
want to pay for or maintain.
