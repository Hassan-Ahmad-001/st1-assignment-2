# SmartCare Domain Modeling

## Part A – Requirements Review

### Nouns

Patient, practitioner, appointment, schedule, name, contact details, specialty,
date and time, status, history, receptionist.

### Verbs

Create, search, book, prevent, view, update, reschedule, maintain, reject.

### Business rules

| Rule                                                                          | Requirement |
|-------------------------------------------------------------------------------|-------------|
| A practitioner cannot hold two appointments at the same time                  | FR-05       |
| A booking must have a patient, a practitioner and a time                      | FR-11       |
| A status is one of booked, completed, cancelled or no-show                    | FR-07       |
| Cancelling an appointment frees its time slot for another booking             | US-04       |
| An appointment already marked completed cannot be cancelled                   | US-04       |
| Past appointments are kept rather than deleted                                | FR-09       |
| Conflicting bookings are blocked in the data, not only warned about on screen | NFR-03      |
| Patient data is open only to authorised clinic staff                          | NFR-06      |

## Part B – Candidate Classes

Candidate classes are listed in the **Requirement-to-Concept Trace** in
`Stage_3_SmartCare_v03_Domain_Model_Workbook.md`.

Four became classes: Patient, Practitioner, Appointment and Schedule. FR-02 and
FR-06 produced no class, because searching a list and listing one day's
appointments need nothing remembered.

## Part C – CRC Cards

### Patient

| Responsibilities                                                                       | Collaborators |
|----------------------------------------------------------------------------------------|---------------|
| Know its own name and contact details, and update them when they change (FR-01, FR-12) | None          |
| Provide its history of past appointments (FR-09)                                       | Appointment   |

### Practitioner

| Responsibilities                                                  | Collaborators         |
|-------------------------------------------------------------------|-----------------------|
| Know its own name and specialty (FR-03)                           | None                  |
| Hold its own appointments and provide its schedule (FR-04, FR-10) | Appointment, Schedule |

### Appointment

| Responsibilities                                                                                               | Collaborators         |
|----------------------------------------------------------------------------------------------------------------|-----------------------|
| Know its patient, its practitioner and its date and time, and refuse to exist without all three (FR-04, FR-11) | Patient, Practitioner |
| Know its own status and change it when asked (FR-07)                                                           | None                  |

### Schedule

| Responsibilities                                                                         | Collaborators             |
|------------------------------------------------------------------------------------------|---------------------------|
| Answer whether its practitioner is already booked at a given time (FR-05, FR-08, NFR-03) | Practitioner, Appointment |
| List its practitioner's upcoming appointments in time order (FR-10)                      | Practitioner, Appointment |

## Part D – UML Model

The diagram and its multiplicities are in the **UML Class Diagram** section of
`Stage_3_SmartCare_v03_Domain_Model_Workbook.md`.

There are three relationships. Patient to Appointment and Practitioner to
Appointment are both one to many. Practitioner to Schedule is one to one.

## Part E – AI Design Review

**Prompt used** (Microsoft Copilot, a UC-approved GenAI tool):

> Act as a software designer. I have attached the confirmed SmartCare
> requirements and the draft class model I produced from them. Suggest any
> classes or relationships the model is missing or has wrong. Use only what
> these requirements state. For every suggestion, name the requirement ID it
> comes from. If you cannot name one, say so.

**What the AI suggested:**

| #  | Suggestion                                | Requirement ID named |
|----|-------------------------------------------|----------------------|
| 1  | Allow a patient to have zero appointments | FR-01                |
| 2  | An AppointmentManager class               | FR-05                |
| 3  | A NotificationManager class               | None                 |
| 4  | A ClinicController class                  | None                 |
| 5  | A ScheduleEngine class                    | None                 |

Asking for requirement IDs is what made the review useful. Three of the five
came back with none.

## Part F – Compare and Decide

The decision on each suggestion is recorded in the **AI Design Review Record** in
`Stage_3_SmartCare_v03_Domain_Model_Workbook.md`, with the reason and what changed in
the model.

| Decision | Count | Suggestion                                            |
|----------|-------|-------------------------------------------------------|
| Accepted | 1     | Allow a patient to have zero appointments             |
| Modified | 1     | An AppointmentManager class                           |
| Rejected | 3     | NotificationManager, ClinicController, ScheduleEngine |

The two suggestions that named a requirement are the two that survived. The
three that named none were all rejected.

## Part G – Python Skeletons

```python
class Patient:
    def __init__(self, name, contact_details):
        self.name = name
        self.contact_details = contact_details
        self.appointments = []

    def update_contact_details(self, contact_details):
        pass

    def appointment_history(self):
        pass


class Practitioner:
    def __init__(self, name, specialty):
        self.name = name
        self.specialty = specialty
        self.appointments = []
        self.schedule = Schedule(self)


class Appointment:
    def __init__(self, patient, practitioner, date_time):
        self.patient = patient
        self.practitioner = practitioner
        self.date_time = date_time
        self.status = "booked"

    def change_status(self, status):
        pass

    def reschedule(self, date_time):
        pass


class Schedule:
    def __init__(self, practitioner):
        self.practitioner = practitioner

    def is_time_taken(self, date_time):
        pass

    def upcoming(self):
        pass
```

## Part H – Consistency Check

| Model element                                  | In the code? | Note                                          |
|------------------------------------------------|--------------|-----------------------------------------------|
| Patient, Practitioner, Appointment, Schedule   | Yes          | One class each, same names                    |
| Patient: name, contact details                 | Yes          | Set when a patient is created                 |
| Practitioner: name, specialty                  | Yes          | Set when a practitioner is created            |
| Appointment: date and time, status             | Yes          | Status starts at booked                       |
| Patient to Appointment, 1 to 0..\*             | Yes          | `Patient.appointments` starts empty           |
| Practitioner to Appointment, 1 to 0..\*        | Yes          | `Practitioner.appointments` starts empty      |
| Practitioner to Schedule, 1 to 1               | Yes          | A schedule is created with the practitioner   |
| Appointment knows its patient and practitioner | Yes          | Both passed in when it is created             |
| FR-05 clash check                              | Named only   | `is_time_taken()` exists but does nothing yet |
| FR-11 refusing an incomplete booking           | Not yet      | No check on the values passed in              |

## Reflection

The hardest decision was whether Schedule should exist. Patient, Practitioner
and Appointment came straight out of the requirements, but FR-05 says a booking
must be refused when a practitioner is already busy, and no single appointment
can answer that, because it only knows about itself. Practitioner could have
done the check, and I left it there at first. I moved it because Practitioner
would then have been doing two jobs at once: describing a person, and checking
a diary. NFR-03 settled it, because blocking a clash in the data rather than
warning about it on screen means the check has to sit inside a class.

The AI over-designed by offering a manager or a controller for almost every
noun. Three of its five suggestions named no requirement at all. Two of those,
NotificationManager and ScheduleEngine, would have added work the client has
never asked for. Reminders are still provisional, and nothing asks the system
to generate appointment times.

The evidence for my final choices was the requirement IDs. Schedule exists
because of FR-05 and NFR-03. A patient can have zero appointments because FR-01 creates a
patient record before any booking exists. FR-02 and FR-06 produced no class
because neither needs anything remembered. Every class I kept can be traced
back to a requirement that asked for it.
