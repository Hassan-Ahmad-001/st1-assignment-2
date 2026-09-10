# Human vs AI Comparison

| Question | Human version | AI version |
|---|---|---|
| Easy to understand? | Yes. It uses simple lists, dictionaries and functions. | Yes. The beginner-friendly version is also simple. |
| Runs successfully? | Yes, for the normal inputs provided. | Yes, for the normal inputs provided. |
| Uses only required features? | Yes. | Yes. |
| Adds assumptions? | Some fixed sample names and times are used. | May add validation or structure that was not explicitly requested. |
| Handles errors? | Yes, it checks for a blank patient name. | Yes, if validation is included. |
| Could I explain it? | Yes. I wrote/understood the basic structure before using AI. | Yes, after reviewing and testing it. |

## At least five limitations identified

1. The program uses hard-coded sample appointments rather than user input.
2. It does not use a database, so data is lost when the program stops.
3. It does not prevent two appointments for the same practitioner at the same time.
4. It only validates the patient name; practitioner name and appointment time are not properly validated.
5. It does not provide functions for cancelling or changing an appointment.
6. It does not provide patient search functionality.
7. The appointment time is stored as plain text, so invalid date/time formats can be entered.
8. There is no login, access control, audit trail, or security mechanism.
