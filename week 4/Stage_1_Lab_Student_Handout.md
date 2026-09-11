# Stage 1 Lab – Human vs AI: Building the SmartCare Prototype

## Part A – Understand the Problem

### What data must be stored?

Patient name, practitioner name, and the date and time of the appointment.
Nothing else has been asked for yet.

### What functions might be useful?

One to record an appointment, and one to display the appointments recorded so
far. Cancelling, changing and searching would be needed later, but not for
this version.

### What could go wrong?

An appointment could be saved with details missing. The same practitioner
could be booked twice at the same time. The appointment time is stored as
text, so an invalid date can be entered. All appointments are lost when the
program stops.

### What requirements are unclear?

Which details are required for each record, who is allowed to use the system
and what they may do, what happens when an appointment is canceled or
changed, and what privacy rules the clinic must meet.

---

## Part B – Build a Human-Written Prototype

`smartcare_v01.py` — both versions: separate variables and print statements,
then a list of dictionaries with `book_appointment()` and
`display_appointments()`.

The five or more limitations asked for at the end of this part are in
`comparison.md`, under "Limitations identified".

---

## Part C – Use AI as Tutor

`ai_usage.md`, section "AI as tutor".

---

## Part D – Generate an Alternative

`ai_usage.md`, section "AI-generated alternative".

---

## Part E – Compare Human and AI Versions

`comparison.md`, section "Comparison".

---

## Part F – Verify Behavior

`comparison.md`, section "Behavior tested on both versions" — both versions
side by side.

`ai_usage.md`, section "Verification" — the AI-generated function on its own.

---

## Part G – Improve One Thing

`reflection.md`, paragraph 4 — the improvement chosen is refusing a booking
when the patient name is empty.

The code for it is the `ValueError` in `book_appointment()` in
`smartcare_v01.py`.

---

## Part H – Reflection

`reflection.md`.

---

## Submission checklist

| Item                             | Where                                                         |
|----------------------------------|---------------------------------------------------------------|
| Python file runs                 | `smartcare_v01.py`                                            |
| Comparison table completed       | `comparison.md`                                               |
| Normal and unusual inputs tested | `comparison.md`, `ai_usage.md`                                |
| AI assistance documented         | `ai_usage.md`                                                 |
| Reflection completed             | `reflection.md`                                               |
| I can explain my code            | Part A above, and the two questions answered in `ai_usage.md` |
