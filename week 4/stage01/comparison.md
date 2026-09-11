# Human vs AI Comparison

The human version is the prototype in `smartcare_v01.py`. The AI version is the function Copilot produced when asked for a beginner-friendly alternative, recorded in `ai_usage.md`.

---

## Comparison

| Question                     | Human version                                 | AI version                                    |
|------------------------------|-----------------------------------------------|-----------------------------------------------|
| Easy to understand?          | Yes, simple lists, dictionaries and functions | Yes, though it prints more than was asked for |
| Runs successfully?           | Yes, for normal inputs                        | Yes for normal inputs, crashes on `None`      |
| Uses only required features? | Yes                                           | Mostly, but added a total and numbering       |
| Adds assumptions?            | Fixed sample names and times                  | Assumes empty always means `""`               |
| Handles errors?              | Raises `ValueError`, stores nothing           | Checks empty strings only, not `None`         |
| Could I explain it?          | Yes, I wrote it                               | Yes, after reading and testing it             |

---

## Behavior tested on both versions

| Test                             | Human version                | AI version                       |
|----------------------------------|------------------------------|----------------------------------|
| Normal appointment               | Stored and displayed         | Stored and listed                |
| Blank patient name               | `ValueError`, nothing stored | Message printed, nothing stored  |
| Same practitioner and time twice | Accepted                     | Accepted                         |
| `patient_name=None`              | `ValueError`, nothing stored | `TypeError`, record left in list |
| `appointment_time=None`          | Accepted, stored as `None`   | Accepted, stored as `None`       |

The clearest difference is the `None` patient name. The human version rejects it before anything is saved. The AI version reads as though it validates every field, but its check only compares against an empty string, so `None` gets past it and the function fails part-way through.

Neither version prevents a double booking.

---

## Limitations identified

1. The program uses hard-coded sample appointments rather than user input.
2. It does not use a database, so data is lost when the program stops.
3. It does not prevent two appointments for the same practitioner at the same time.
4. It only validates the patient name; practitioner name and appointment time are not properly validated.
5. It does not provide functions for cancelling or changing an appointment.
6. It does not provide patient search functionality.
7. The appointment time is stored as plain text, so invalid date/time formats can be entered.
8. There is no login, access control, audit trail, or security mechanism.
