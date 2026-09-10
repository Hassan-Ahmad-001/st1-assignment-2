# Stage 1 | Introducing Software Technology
## Case Study with Python and Guided AI Use — Completed Handout

## Learning goals

- Explain why software engineering is broader than coding.
- Identify stakeholders in a simple software problem.
- Recognise missing requirements.
- Critically evaluate AI-generated feature suggestions.
- Explain why AI output should not automatically be treated as correct.

## Activity 1 – Think-Pair-Share (10 minutes)

If ChatGPT or Copilot can produce a 100-line Python application very quickly, what knowledge does a software engineer still need?

1. **Understanding the real problem and requirements:** talking to stakeholders to find out what is needed, since AI can only respond to the prompt it is given, not to unstated needs or context.
2. **System-level judgement:** knowing how the code fits into a larger system, architecture, data storage, security, integration with existing tools, and how it will be maintained over time.
3. **Critical evaluation and accountability:** the ability to read, test, and verify that AI-generated code is correct, secure, and appropriate for the situation, and to take responsibility for the decision to use it.

## Activity 2 – Is This Software Engineering? (10 minutes)

- **Scenario A:** A student writes a 50-line Python calculator.
- **Scenario B:** A team develops a payroll system used by 5,000 employees.
- **Scenario C:** An AI assistant generates a simple appointment application from one prompt.

| Scenario | Programming? | Software engineering? | Why? |
|---|---|---|---|
| A | Yes | No | One person, no stakeholders, no requirements process, low risk if wrong, no ongoing maintenance; it's just writing code. |
| B | Yes | Yes | Multiple stakeholders, high risk (people's pay), needs requirements, design, testing, security, compliance, teamwork and long-term maintenance. |
| C | Yes | No | A single prompt with no requirements gathering, stakeholder input, scope checking, or verification of correctness; output wasn't engineered, just generated. |

## Activity 3 – SmartCare Problem Analysis (20 minutes)

**Client statement:** SmartCare Community Clinic currently uses spreadsheets and paper records to manage patients and appointments. The clinic wants new software to improve these processes.

### Task 1 – Identify stakeholders

| Stakeholder | What do they need? |
|---|---|
| **Receptionists/admin staff** | A fast, reliable way to book, find, and update appointments without duplicate entry across paper and spreadsheets. |
| **Doctors/clinicians** | Quick, accurate access to patient records and schedules, ideally on the day of the appointment. |
| **Patients** | An easy way to book, reschedule, or cancel appointments, and confidence that their health data is kept private and secure. |
| **Clinic management** | Reporting on appointments, staff workload, and patient numbers, plus assurance that the system meets healthcare record-keeping and privacy regulations. |

### Task 2 – Identify current problems

1. Paper records can be lost, damaged, misfiled, or duplicated, and are hard to search quickly.
2. Spreadsheets don't enforce consistency, double bookings, typos, and conflicting versions are easy to create with multiple people editing.
3. There is no easy way for staff in different roles (reception, clinicians, management) to see the same up-to-date information at the same time.
4. Sensitive patient data on paper and in shared spreadsheets has weak security and no audit trail of who accessed or changed what.

### Task 3 – Ask client questions

1. Roughly how many patients and appointments does the clinic handle per day/week, and how many staff would use the system?
2. Who needs access to which information; should reception, clinicians, and management see different things?
3. Are there specific healthcare privacy or record-keeping regulations the system must comply with?
4. Does the system need to integrate with any existing tools (e.g. billing software, government health databases)?
5. What is the budget and timeframe, and is this a one-off building or an ongoing support system?

## Activity 4 – Critique an AI Response (15 minutes)

An AI assistant suggests:

- appointment management
- facial-recognition login
- AI diagnosis recommendations
- patient search
- online payment
- practitioner schedule view
- insurance processing
- automatic treatment-plan generation

| Suggestion | Client evidence? | In scope? | Decision |
|---|---|---|---|
| Appointment management | Yes | Yes | Include |
| Facial recognition login | No | No | Reject: unnecessary complexity/cost and privacy risk for a small clinic; client never asked for biometric security. |
| AI diagnosis recommendations | No | No | Reject: clinical decision-making is outside scope and carries serious safety/liability risk; not a record/booking problem. |
| Patient search | Implied by patient management | Yes | Include |
| Online payment | Not mentioned | Unclear | Ask the client: useful but not stated; confirm before building. |
| Practitioner schedule view | Implied by appointment management | Yes | Include |
| Insurance processing | Not mentioned | Unclear | Ask the client: maybe a much larger integration than described; needs confirmation. |
| Treatment-plan generation | No | No | Reject: clinical content generated automatically is a safety risk and not requested. |

## Exit question

Write one activity that a software engineer must perform, and that cannot safely be delegated entirely to AI.

**Answer:** Gathering and validating requirements with real stakeholders, asking clarifying questions, weighing trade-offs, and deciding what is in scope. This requires judgement, context about the client's real needs, and decision-making; an AI can suggest options, but it cannot be responsible for whether they are correct, safe, or wanted.
