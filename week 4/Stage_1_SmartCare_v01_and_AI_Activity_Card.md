# SmartCare v0.1 – Initial Engineering Brief and AI Activity Card

## SmartCare scenario

SmartCare Community Clinic currently uses spreadsheets and paper records to manage patients and appointments. The client says:

> "We need software to help manage patients, practitioners and appointments."

This is not yet a complete specification.

---

# Initial Engineering Brief

## 1. Problem Summary

SmartCare Community Clinic currently relies on spreadsheets and paper-based records to manage patients, practitioners and appointments. This approach can make information difficult to find, update and keep consistent. It may also increase the risk of duplicate records, missed appointments and administrative errors. The clinic needs a software system that can organize patient, practitioner and appointment information in one place. The initial goal is to make everyday clinic administration easier and more reliable. However, the client has only provided a high-level request, so more information is required before the full system requirements, workflows, permissions and technical solution can be confirmed.

---

## 2. Initial Stakeholders

| Stakeholder                      | Possible Need                                                              |
|----------------------------------|----------------------------------------------------------------------------|
| Patients                         | Accurate personal details and correctly scheduled appointments             |
| Practitioners                    | Access to their appointment schedules and relevant patient information     |
| Reception / administrative staff | Create, update, search and manage patients, practitioners and appointments |
| Clinic manager                   | Reliable records and visibility of clinic operations                       |



---

## 3. Initial Features

| Feature                                   | Confirmed or Provisional? | Why?                                                                                                 |
|-------------------------------------------|---------------------------|------------------------------------------------------------------------------------------------------|
| Manage patient records                    | Confirmed                 | The client explicitly says the software must help manage patients.                                   |
| Manage practitioner records               | Confirmed                 | The client explicitly says the software must help manage practitioners.                              |
| Manage appointments                       | Confirmed                 | The client explicitly says the software must help manage appointments.                               |
| Add, edit and search records              | Provisional               | These are likely required for record management, but the client has not specifically confirmed them. |
| Appointment rescheduling and cancellation | Provisional               | Common appointment functions, but not stated in the brief.                                           |
| User login and role-based access          | Provisional               | Likely important for a clinic system, but no security requirements are given.                        |
| Appointment reminders                     | Provisional               | Could be useful, but the client has not requested SMS, email or other reminders.                     |
| Reports                                   | Provisional               | Managers may need reports, but reporting requirements are not stated.                                |
| Data backup and recovery                  | Provisional               | Important for operational reliability, but not described in the initial brief.                       |

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

The code is a simple SmartCare appointment prototype written in Python. The first part stores the details of two appointments in separate variables for the patient name, practitioner name and appointment time, and prints a welcome message followed by one line for each appointment. The second part does the same job using a list, dictionaries and functions. The appointments are stored in a list, the book_appointment() function checks that the patient name is not empty and then stores the patient, practitioner and time as a dictionary in that list, and the display_appointments() function prints each stored appointment or a message if there are none. When the file is run, the welcome message and both appointments are printed twice, because the second part books the same two appointments again.

### What problems can I already identify?

Reading the code, the welcome message and both appointments are printed twice, because the second part books the same two appointments the first part has already printed. Only the patient name is checked, so the practitioner name and the appointment time can be left empty and the booking is still stored. Nothing stops the same practitioner being booked twice at the same time, and the appointment time is stored as plain text, so any value can be entered. The appointments are held in a list that is lost when the program ends, and there is no way to cancel a booking, change one, or search for a patient.

Behind the code, the requirements are still incomplete. The client has only stated that the system should manage patients, practitioners and appointments, so data fields, user roles, permissions, workflows and security requirements remain unknown.

---

## AI Request

The code was given to Microsoft Copilot along with the tutor prompt:

> Act as a tutor. Explain this code and identify potential problems. Do not provide a complete replacement. Ask me questions that help me reason about the solution.

Copilot explained the code correctly and then made the following main points:

- The requirements should be clarified before any further features are added, because the prototype currently assumes what a booking needs to contain.
- The system needs to know who its users are and what each of them is allowed to do.
- The information held for a patient, a practitioner and an appointment should be properly defined instead of being stored as three pieces of text.
- It offered to produce a complete final version of the program.

It also asked me two questions: what should happen if the same practitioner is booked twice at the same time, and what information the clinic actually needs to keep for each appointment.

---

## Evaluate

| Suggestion                                                         | Useful | Unclear | Incorrect | Out of Scope |
|--------------------------------------------------------------------|-------:|--------:|----------:|-------------:|
| Clarify system requirements before implementation                  |     ✓ |         |           |              |
| Identify the required users and permissions                        |     ✓ |         |           |              |
| Define patient, practitioner and appointment data                  |     ✓ |         |           |              |
| Produce a complete final program before requirements are confirmed |        |         |           |           ✓ |

---

## Decide

| Significant Suggestion               | Decision        | Reason                                                                    |
|--------------------------------------|-----------------|---------------------------------------------------------------------------|
| Clarify requirements with the client | Accept          | The current brief is incomplete.                                          |
| Define the main data entities        | Accept          | Patients, practitioners and appointments are explicitly mentioned.        |
| Add security and role-based access   | Keep unverified | It is likely necessary, but the client has not yet confirmed the details. |
| Add reminders and reporting          | Keep unverified | These may be useful but are not part of the confirmed requirements.       |

---

## Verify

I checked the AI response against the code itself, and recorded what actually happened.

- **Run the code.** The file runs to the end without any errors. The welcome message and both appointments are printed twice, because the second part books the same two appointments that the first part has already printed.
- **Test normal input.** Booking Alice Smith with Dr. John Doe at the given date and time is stored correctly and displayed on one line in the expected format.
- **Test unusual input.** An empty patient name and a patient name of None are both rejected with a ValueError, as expected. A patient name made up only of spaces is accepted and displayed as a blank name. An empty practitioner name and an empty appointment time are also accepted. The same practitioner can be booked twice at the same date and time, and a value such as "not-a-date" is accepted as an appointment time.
- **Compare with requirements.** The prototype covers the three confirmed areas, but only as plain text inside a single appointment record, so patients and practitioners cannot be looked up or updated on their own. It also allows the duplicate booking that the clinic reported as a problem, so it meets the Stage 1 programming goal but not the clinic's confirmed needs.
- **Ask tutor/peer.** The questions I still want to raise are whether the validation should stay inside the booking function or move into a separate checking function, and whether the appointment time should be stored as a proper date and time value at this stage.
- **Check documentation.** The Python documentation on dictionaries and on raising exceptions explained why a name of spaces is accepted, the check tests whether the value is empty, and a string of spaces is not empty. Removing the spaces before the check would be needed to catch it.

---

## Explain

At this stage, I can explain that the proposed SmartCare system is intended to replace or reduce the clinic's reliance on spreadsheets and paper records by organizing patient, practitioner and appointment information in software without reading AI response.

I still need to understand the detailed requirements before I can explain a final implementation. In particular, I need clarification about user roles, required data fields, appointment rules, security and privacy requirements, reporting, integrations and the technology that will be used to build the system.

---

## Conclusion

The Stage 1 brief identifies three confirmed areas: **patients, practitioners and appointments**. Everything beyond these high-level needs should be treated as provisional until clarified with the client. The next engineering step should therefore focus on requirements gathering before detailed design or coding begins.

---

## References

- The formatting reference was taken from *https://mdstill.com/markdown-guide*
