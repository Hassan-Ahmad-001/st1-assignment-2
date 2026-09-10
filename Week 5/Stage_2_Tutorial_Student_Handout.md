# Stage 2 Tutorial — Week 5 | 60 minutes
### Completed Handout

## Learning goals

- Analyse stakeholders.
- Distinguish functional and non-functional requirements.
- Recognise ambiguity and unsupported requirements.
- Define scope.
- Develop user stories and acceptance criteria.
- Critique AI-generated requirements.

## Activity 1 – Stakeholder Map

| Stakeholder | Need | Potential conflict |
|---|---|---|
| Receptionists / admin staff | Fast booking, search, and cancellation with as few steps as possible. | May want fewer validation checks for speed, which can clash with management's need for strict data integrity and an audit trail. |
| Practitioners | An accurate, up-to-date schedule with no double-bookings. | May want more control over blocking out their own time, which can conflict with reception's need for flexible, fast booking. |
| Patients | Easy booking and confidence their information is private. | May want fast self-service booking, which conflicts with the current provisional decision to keep booking staff-mediated only. |
| Clinic management | Reporting, compliance, and a system that's cheap to maintain. | May want stricter access control and audit logging, which can slow down reception's day-to-day workflow. |

## Activity 2 – Functional or Non-Functional?

- ☑ **Functional** — The system shall allow staff to cancel an appointment.
  *Describes an observable capability the system must have.*
- ☑ **Non-functional** — The system should remain responsive for the course-scale dataset.
  *Describes a quality (performance), not a specific capability.*
- ☑ **Functional** — The system shall retain cancelled appointments.
  *Describes what data the system must keep, an observable capability.*
- ☑ **Non-functional** — Core business logic should be independently testable.
  *Describes a quality (testability/maintainability), not a user-facing capability.*
- ☑ **Functional** — The system shall search for a patient by ID.
  *Describes an observable capability.*

## Activity 3 – Repair Ambiguous Requirements

**"The system should be easy to use."**
- **Problem:** "Easy" is subjective and not measurable — there's no way to test whether it's been met.
- **Clarification question:** What specific tasks must a first-time user complete without training, and in how many steps or minutes?

**"Patient search should be fast."**
- **Problem:** No target response time or dataset size is given, so "fast" can't be verified.
- **Clarification question:** What is the maximum acceptable response time for a patient search, and for roughly how many patient records?

**"The system should securely manage data."**
- **Problem:** "Securely" doesn't specify which controls or standards apply, so it can't be designed or tested against.
- **Clarification question:** What specific security controls (authentication, access levels, encryption) are required, and are there compliance standards the system must meet?

**"Appointments should normally be easy to cancel."**
- **Problem:** "Normally" implies unstated exceptions, and "easy" is unmeasurable — both leave the requirement open to interpretation.
- **Clarification question:** Are there cases where cancellation should be restricted (e.g. an already-completed appointment), and what does "easy" mean in number of steps?

## Activity 4 – AI Requirements Audit

| AI suggestion | Classification | Evidence / reason |
|---|---|---|
| Patients receive SMS reminders. | Assumption requiring validation | Plausible and common in booking systems, but not mentioned in the client brief — needs client confirmation before being added. |
| Facial recognition login. | Unsupported | No evidence in the brief; disproportionate complexity, cost, and privacy risk for a small clinic system. |
| Receptionists create appointments. | Confirmed | Directly stated in the client brief as a core need (staff report duplicate bookings, implying booking is a staff task). |
| Online payment. | Out of scope | Billing/payment was explicitly excluded from scope in the requirements specification — not part of the current problem the client described. |
| Practitioners view schedules. | Confirmed | Directly supports the brief's mention of inconsistent appointment status and follows from staff needing to manage appointments. |
| AI recommends treatments. | Out of scope | Clinical decision-making was explicitly excluded from scope; also unsupported by any evidence in the brief and carries real safety/liability risk. |
| Cancelled appointments remain in history. | Confirmed | Matches the brief's complaint about "limited appointment history" and the functional requirement to retain appointment records. |

## Exit question

**Why is 'AI suggested it' not sufficient evidence for a requirement?**

Because an AI's suggestion is a plausible-sounding pattern drawn from its
training data, not knowledge of this client's actual needs, budget,
priorities, or constraints. A requirement needs to be traceable to real
evidence — something a stakeholder said, a problem stated in the brief, or
a business goal — otherwise the team risks building features nobody asked
for, missing what was actually needed, or introducing real risk (like
AI-generated treatment recommendations) that no one with authority actually
approved. Treating "AI suggested it" as sufficient evidence quietly shifts
responsibility for a design decision onto a tool that can't be held
accountable for the outcome.
