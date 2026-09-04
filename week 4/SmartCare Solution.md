# SmartCare v0.1 – Initial Engineering Brief and AI Activity Card

## A2 Case Study – Stage 1

### SmartCare Scenario

SmartCare Community Clinic currently uses spreadsheets and paper records to manage patients and appointments. The client says:

> “We need software to help manage patients, practitioners and appointments.”

This is an initial requirement only, so some features below are **provisional** until the client confirms them.

---

# Initial Engineering Brief

## 1. Problem Summary

SmartCare Community Clinic currently relies on spreadsheets and paper-based records to manage patients, practitioners and appointments. This approach can make information difficult to find, update and keep consistent. It may also increase the risk of duplicate records, missed appointments and administrative errors. The clinic needs a software system that can organise patient, practitioner and appointment information in one place. The initial goal is to make everyday clinic administration easier and more reliable. However, the client has only provided a high-level request, so more information is required before the full system requirements, workflows, permissions and technical solution can be confirmed.

---

## 2. Initial Stakeholders

| Stakeholder | Possible Need |
|---|---|
| Patients | Accurate personal details and correctly scheduled appointments |
| Practitioners | Access to their appointment schedules and relevant patient information |
| Reception / administrative staff | Create, update, search and manage patients, practitioners and appointments |
| Clinic manager | Reliable records and visibility of clinic operations |
| System administrator / IT support | Maintain user accounts, security, backups and system availability |

> These needs are provisional because the original brief does not describe specific user roles or permissions.

---

## 3. Initial Features

| Feature | Confirmed or Provisional? | Why? |
|---|---|---|
| Manage patient records | Confirmed | The client explicitly says the software must help manage patients. |
| Manage practitioner records | Confirmed | The client explicitly says the software must help manage practitioners. |
| Manage appointments | Confirmed | The client explicitly says the software must help manage appointments. |
| Add, edit and search records | Provisional | These are likely required for record management, but the client has not specifically confirmed them. |
| Appointment rescheduling and cancellation | Provisional | Common appointment functions, but not stated in the brief. |
| User login and role-based access | Provisional | Likely important for a clinic system, but no security requirements are given. |
| Appointment reminders | Provisional | Could be useful, but the client has not requested SMS, email or other reminders. |
| Reports | Provisional | Managers may need reports, but reporting requirements are not stated. |
| Data backup and recovery | Provisional | Important for operational reliability, but not described in the initial brief. |

---

## 4. Questions for the Client

1. What information must be stored for each patient, practitioner and appointment?
2. Which staff members will use the system, and what actions should each type of user be allowed to perform?
3. What is the exact appointment workflow, including booking, rescheduling, cancellation and completion?
4. Does the clinic require appointment reminders, notifications, reports or integrations with any existing systems?
5. What security, privacy, backup and record-retention requirements must the system meet?

---

## 5. What We Do Not Yet Know

1. The detailed data fields, validation rules and relationships required for patient, practitioner and appointment records.
2. The required user roles, permissions, security controls and privacy rules.
3. The detailed workflows, technical platform, integrations, reporting needs and non-functional requirements such as performance, availability and backup.

---

# AI Activity Card – Ask, Check, Explain

## Before AI

### What do I think the code does?

No program code is included in the provided SmartCare Stage 1 resource, so the behaviour of any code cannot yet be analysed.

### What problems can I already identify?

The main problem at this stage is incomplete requirements rather than a coding problem. The client has only stated that the system should manage patients, practitioners and appointments. Important details such as data fields, user roles, permissions, workflows, validation rules and security requirements are still unknown.

---

## AI Request

**Prompt:**

> Act as a tutor. Explain this code and identify potential problems. Do not provide a complete replacement. Ask me questions that help me reason about the solution.

This prompt should be used once the relevant SmartCare code is provided.

---

## Evaluate

Because no code or AI-generated code review is included in the resource, there are currently no technical AI suggestions to evaluate.

| Suggestion | Useful | Unclear | Incorrect | Out of Scope |
|---|---:|---:|---:|---:|
| Clarify system requirements before implementation | ✓ |  |  |  |
| Identify the required users and permissions | ✓ |  |  |  |
| Define patient, practitioner and appointment data | ✓ |  |  |  |
| Produce a complete final program before requirements are confirmed |  |  |  | ✓ |

---

## Decide

| Significant Suggestion | Decision | Reason |
|---|---|---|
| Clarify requirements with the client | Accept | The current brief is incomplete. |
| Define the main data entities | Accept | Patients, practitioners and appointments are explicitly mentioned. |
| Add security and role-based access | Keep unverified | It is likely necessary, but the client has not yet confirmed the details. |
| Add reminders and reporting | Keep unverified | These may be useful but are not part of the confirmed requirements. |

---

## Verify

When code is later developed, verification should include:

- Run the code and confirm that it executes successfully.
- Test normal inputs such as valid patient and appointment details.
- Test unusual or invalid inputs such as missing fields, duplicate records and invalid appointment times.
- Compare the implemented behaviour with the confirmed client requirements.
- Ask a tutor or peer to review uncertain design or coding decisions.
- Check the relevant programming language, framework and library documentation.

---

## Explain

At this stage, I can explain that the proposed SmartCare system is intended to replace or reduce the clinic's reliance on spreadsheets and paper records by organising patient, practitioner and appointment information in software.

I still need to understand the detailed requirements before I can explain a final implementation. In particular, I need clarification about user roles, required data fields, appointment rules, security and privacy requirements, reporting, integrations and the technology that will be used to build the system.

---

## Conclusion

The Stage 1 brief identifies three confirmed areas: **patients, practitioners and appointments**. Everything beyond these high-level needs should be treated as provisional until clarified with the client. The next engineering step should therefore focus on requirements gathering before detailed design or coding begins.

***


## References

- The formatting reference was taken from *https://mdstill.com/markdown-guide*
- I did used AI for taking ideas and how can I solve this, and also I used it for proper formatting.