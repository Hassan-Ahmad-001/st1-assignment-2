# AI Usage Documentation

## AI tool used

Microsoft Copilot.

## Purpose of AI use

AI was used as a Python tutor and to generate an alternative beginner-friendly appointment-booking function.

---

## AI as tutor

**Prompt used:**

> Act as a Python tutor.
> I am learning introductory software technology.
> Here is a small appointment-booking function.
> 1. Explain what the code does.
> 2. Identify three limitations.
> 3. Suggest improvements.
> 4. Do not rewrite the whole application.
> 5. Ask me two questions to test my understanding.

### What Copilot explained

The code does the same job twice. The first version keeps each appointment in its own variables and prints them. The second version keeps each appointment as a dictionary inside a list, adds to that list with `book_appointment()` and prints it with `display_appointments()`.

It pointed out that the `ValueError` stops an appointment with no patient name from being saved.

### Three limitations Copilot identified

| # | Limitation                                                                | Confirmed? |
|---|---------------------------------------------------------------------------|------------|
| 1 | Appointments are hard-coded rather than entered by a user                 | Yes        |
| 2 | Two appointments can be booked for the same practitioner at the same time | Yes        |
| 3 | Only the patient name is validated                                        | Yes        |

I checked each one rather than taking them on trust. For limitation 2, I booked Dr. John Doe twice at 2024-07-20 10:00 AM and both records were stored. For limitation 3, an empty practitioner name and an empty appointment time were both accepted.

### Improvements Copilot suggested

- Clarify the requirements before adding further features.
- Establish who the users are and what each is allowed to do.
- Define the information held for each record properly, rather than storing three pieces of text.

It also offered to write a complete final version of the program. I did not accept, because instruction 4 ruled it out and the requirements are not yet confirmed.

### The two questions Copilot asked, and my answers

**What should happen if the same practitioner is booked twice at the same time?**
The booking should be refused and the receptionist told which appointment it clashes with. A duplicate booking is one of the problems the clinic reported.

**What information does the clinic need to keep for each appointment?**
I do not know yet. The client has named patients, practitioners and appointments but has not said which fields are required, so this is a question for the client rather than something to assume.

---

## AI-generated alternative

**Instruction given:**

> Write a simple beginner-friendly Python function that stores a patient name, a practitioner name and an appointment time. Do not use a database. Do not use a graphical interface.

**Code Copilot produced:**

```python
appointment_list = []


def add_appointment(patient_name, practitioner_name, appointment_time):
    """Store one appointment and return it."""
    if patient_name == "" or practitioner_name == "" or appointment_time == "":
        print("All three details are required.")
        return None

    new_appointment = {
        "patient": patient_name,
        "practitioner": practitioner_name,
        "time": appointment_time
    }
    appointment_list.append(new_appointment)
    print("Appointment added for " + patient_name + ".")
    return new_appointment


def show_appointments():
    """Print every appointment that has been stored."""
    print("Total appointments:", len(appointment_list))
    for number, appointment in enumerate(appointment_list, start=1):
        print(number, "-", appointment["patient"], "with",
              appointment["practitioner"], "at", appointment["time"])
```

The database and interface restrictions were followed. Copilot did add two things that were not asked for: a running total of appointments, and a numbered listing.

---

## Verification

The AI output was run and tested rather than accepted automatically. The same test cases were used as on my own version.

| Test                             | Result                                   |
|----------------------------------|------------------------------------------|
| Normal appointment               | Stored and listed correctly              |
| Blank patient name               | Refused, message printed, nothing stored |
| Same practitioner and time twice | Accepted                                 |
| `patient_name=None`              | Crashes with a `TypeError`               |
| `appointment_time=None`          | Accepted and stored                      |

The `None` patient name is the important one. The empty-string test does not catch `None`, so the record is added to the list and the function then fails while building the confirmation message. The appointment is left stored even though the call failed.

My own version raises a `ValueError` and stores nothing, so on that input the human-written code behaves correctly and the AI-generated code does not. Testing was the only way to find this.
