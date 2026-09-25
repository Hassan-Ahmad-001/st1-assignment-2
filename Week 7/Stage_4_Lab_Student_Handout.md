# Implementing the SmartCare Domain Layer

## Part A - Revisit Approved UML

The approved model holds four classes: Patient, Practitioner, Appointment and Schedule. Each responsibility, attribute and operation is listed in the UML-to-Code Trace in Stage_4_SmartCare_v04_Domain_Implementation_Workbook.md.

The three relationships were confirmed before any code was written.

| Relationship                | Multiplicity | Confirmed                                                                                   |
|-----------------------------|--------------|---------------------------------------------------------------------------------------------|
| Patient to Appointment      | 1 to 0..*    | A patient record exists before any booking, so the lower bound is zero.                     |
| Practitioner to Appointment | 1 to 0..*    | FR-04 books with one named practitioner. A practitioner with nothing booked is still valid. |
| Practitioner to Schedule    | 1 to 1       | Each practitioner has one diary, created with the record.                                   |

Two requirements sit outside these classes. FR-02 searches across all patient records and FR-06 lists the appointments on one day, and both run over a collection rather than belonging to any one object, so neither produced a class to implement here.

The complete implementation is shown across Parts B, C and D, as it stands after the review in Part E and the refactor in Part G.

## Part B - Implement Patient (AI OFF)

Written without AI. Every parameter and return value is annotated, and the domain operations carry one-line docstrings naming the requirement they implement (NFR-04). Name and contact details are validated when the record is created and again whenever they are updated, so neither can be left empty or blank (FR-01, FR-12). Both are readable but cannot be reassigned, and the appointment history is returned as a tuple so a caller cannot remove a record (FR-09).

The exception type and the two validation helpers are shared by every class, so they are shown first.

```python
from __future__ import annotations

from datetime import datetime
from enum import Enum

class DomainError(Exception):
    pass

def _required_text(value: str, field: str) -> str:
    """Return the value stripped, or raise if it is empty."""
    if not isinstance(value, str) or not value.strip():
        raise DomainError(f"{field} must not be empty")
    return value.strip()

def _required_time(value: str, field: str) -> datetime:
    """Return the value as a date and time, or raise (FR-04, FR-11)."""
    text = _required_text(value, field)
    try:
        moment = datetime.fromisoformat(text)
    except ValueError:
        raise DomainError(
            f"{field} must be a date and time, such as 2024-07-20 10:00"
        ) from None
    if " " not in text and "T" not in text:
        raise DomainError(f"{field} must include a time, such as 2024-07-20 10:00")
    return moment
class Patient:
    __slots__ = ("_appointments", "_contact_details", "_name")

    def __init__(self, name: str, contact_details: str) -> None:
        self._name = _required_text(name, "Patient name")
        self._contact_details = _required_text(contact_details, "Contact details")
        self._appointments: list[Appointment] = []

    @property
    def name(self) -> str:
        return self._name

    @property
    def contact_details(self) -> str:
        return self._contact_details

    def update_contact_details(self, contact_details: str) -> None:
        """Replace the stored contact details (FR-12)."""
        self._contact_details = _required_text(contact_details, "Contact details")

    def appointment_history(self) -> tuple[Appointment, ...]:
        """Return past and current appointments, cancelled ones kept (FR-09)."""
        return tuple(self._appointments)

    def add_appointment(self, appointment: Appointment) -> None:
        """Attach an appointment that already names this patient (FR-09)."""
        if appointment.patient is not self:
            raise DomainError("That appointment is not for this patient")
        if appointment not in self._appointments:
            self._appointments.append(appointment)
```

## Part C - Implement Practitioner (AI OFF)

Written without AI. FR-03 asks for a name and a specialty; an identifier was added during implementation, since two practitioners may share a name. All three are validated the same way and all three are read-only. There is no database logic: nothing here reads or writes storage, so each class can be tested on its own (NFR-05).

```python
class Practitioner:
    __slots__ = (
        "_appointments",
        "_name",
        "_practitioner_id",
        "_schedule",
        "_specialty",
    )

    def __init__(self, practitioner_id: str, name: str, specialty: str) -> None:
        self._practitioner_id = _required_text(practitioner_id, "Practitioner id")
        self._name = _required_text(name, "Practitioner name")
        self._specialty = _required_text(specialty, "Specialty")
        self._appointments: list[Appointment] = []
        self._schedule = Schedule(self)

    @property
    def practitioner_id(self) -> str:
        return self._practitioner_id

    @property
    def name(self) -> str:
        return self._name

    @property
    def specialty(self) -> str:
        return self._specialty

    def schedule(self) -> Schedule:
        """Return this practitioner's diary (FR-10)."""
        return self._schedule

    def appointments(self) -> tuple[Appointment, ...]:
        """Return the appointments booked with this practitioner (FR-04)."""
        return tuple(self._appointments)

    def add_appointment(self, appointment: Appointment) -> None:
        """Attach an appointment that already names this practitioner (FR-04)."""
        if appointment.practitioner is not self:
            raise DomainError("That appointment is not for this practitioner")
        if appointment not in self._appointments:
            self._appointments.append(appointment)
```

### Schedule

Schedule answers the one question that needs several appointments at once, so FR-05 is enforced inside the domain rather than by a caller.

```python
class Schedule:
    __slots__ = ("_practitioner",)

    def __init__(self, practitioner: Practitioner) -> None:
        self._practitioner = practitioner

    def is_time_taken(self, date_time: datetime) -> bool:
        """Return True if the practitioner already holds that time (FR-05)."""
        return any(
            appointment.date_time == date_time
            and appointment.status is not AppointmentStatus.CANCELLED
            for appointment in self._practitioner.appointments()
        )

    def upcoming(self) -> tuple[Appointment, ...]:
        """Return booked appointments in time order (FR-10)."""
        booked = [
            appointment
            for appointment in self._practitioner.appointments()
            if appointment.status is AppointmentStatus.BOOKED
        ]
        return tuple(sorted(booked, key=lambda appointment: appointment.date_time))
```

## Part D - Implement Appointment (AI ON)

**Prompt used (Microsoft Copilot, a UC-approved GenAI tool):**

> Act as a Python pair programmer. Implement only the Appointment class from the approved SmartCare UML. Use type hints and an AppointmentStatus enum. Cancelled appointments remain as objects. Do not add database, UI, notification or service classes. Protect status transitions and explain any decision not directly visible in the UML. Match the style of the classes I have already written: type hints, a one-line docstring on each method, and attributes kept private behind read-only properties. I have attached the UML.

The enum and the single exception were agreed before the prompt was sent. The enum holds exactly the four values FR-07 names.

The code below is the final Appointment implementation, after the review in Part E and the refactor in Part G. What Copilot first produced, and what was changed, are described in those two parts.

```python
class AppointmentStatus(Enum):
    BOOKED = "booked"
    COMPLETED = "completed"
    CANCELLED = "cancelled"
    NO_SHOW = "no-show"
class Appointment:
    __slots__ = ("_date_time", "_patient", "_practitioner", "_status")

    def __init__(
        self, patient: Patient, practitioner: Practitioner, date_time: str
    ) -> None:
        if not isinstance(patient, Patient):
            raise DomainError("An appointment needs a patient")
        if not isinstance(practitioner, Practitioner):
            raise DomainError("An appointment needs a practitioner")
        time = _required_time(date_time, "Appointment time")

        if practitioner.schedule().is_time_taken(time):
            raise DomainError(
                f"{practitioner.name} already has an appointment at {time}"
            )

        self._patient = patient
        self._practitioner = practitioner
        self._date_time = time
        self._status = AppointmentStatus.BOOKED

        patient.add_appointment(self)
        practitioner.add_appointment(self)

    @property
    def patient(self) -> Patient:
        return self._patient

    @property
    def practitioner(self) -> Practitioner:
        return self._practitioner

    @property
    def date_time(self) -> datetime:
        return self._date_time

    @property
    def status(self) -> AppointmentStatus:
        """Return the status. Use the methods below to change it (FR-07)."""
        return self._status

    def _require_booked(self, action: str) -> None:
        if self._status is not AppointmentStatus.BOOKED:
            raise DomainError(
                f"Cannot {action} an appointment that is already "
                f"{self._status.value}"
            )

    def cancel(self) -> None:
        """Cancel a booked appointment, keeping the record (US-04)."""
        self._require_booked("cancel")
        self._status = AppointmentStatus.CANCELLED

    def complete(self) -> None:
        """Mark a booked appointment as completed (FR-07)."""
        self._require_booked("complete")
        self._status = AppointmentStatus.COMPLETED

    def mark_no_show(self) -> None:
        """Mark a booked appointment as a no-show (FR-07)."""
        self._require_booked("mark as a no-show")
        self._status = AppointmentStatus.NO_SHOW

    def reschedule(self, date_time: str) -> None:
        """Move a booked appointment to a free time (FR-08)."""
        self._require_booked("reschedule")
        time = _required_time(date_time, "Appointment time")
        if time != self._date_time and self._practitioner.schedule().is_time_taken(
            time
        ):
            raise DomainError(
                f"{self._practitioner.name} already has an appointment at {time}"
            )
        self._date_time = time
```

## Part E - Review Generated Code

| Checked for | Finding | Action |
|---|---|---|
| Model consistency | The generated class followed the approved UML for attributes and relationships, but departed from it on status typing, status operations, inheritance and an added dependency. | Structure kept. Each departure is dealt with in the rows below. |
| Unsupported features | Inheritance from PatientRecord and a NotificationManager dependency, neither of which appears in the approved model. | Both removed. |
| Public state mutation | The generated version exposed status as a public attribute, so any caller could set it and step around US-04. | Made it readable but not writable. |
| Unnecessary inheritance | The generated version inherited from PatientRecord. | Removed. Appointment holds a reference to a Patient instead. |
| Invented dependencies | The generated version called a NotificationManager inside cancel(). | Removed. Reminders are provisional and unconfirmed. |
| Error handling | The checks for a missing patient, practitioner or time were correct, but they raised built-in errors. | Checks kept. Only the exception type changed to DomainError, so a caller sees one type. |

## Part F - Manual Behaviour Checks

Each check was run against the implementation. The results below are what actually happened.

**Valid objects**

Patient Alice Smith with contact details 0400 000 000, practitioner PR-001 Dr. John Doe in General Practice, and an appointment between them at 2024-07-20 10:00. The appointment was created with status booked.

**Invalid input**

| Input | Result |
|---|---|
| Patient with an empty name | Refused: Patient name must not be empty |
| Patient with a name of spaces only | Refused: Patient name must not be empty |
| Practitioner with no identifier | Refused: Practitioner id must not be empty |
| Appointment with no patient | Refused: An appointment needs a patient |
| Appointment with no practitioner | Refused: An appointment needs a practitioner |
| Appointment with no time | Refused: Appointment time must not be empty |
| Appointment with a time that is not a time | Refused: Appointment time must be a date and time, such as 2024-07-20 10:00 |
| Appointment with a date but no time | Refused: Appointment time must include a time, such as 2024-07-20 10:00 |

**Cancel a scheduled appointment**

Status changed from booked to cancelled. The record stayed in the patient history and in the practitioner list, and the time slot became free for another booking.

**Illegal repeated transition**

| Attempt | Result |
|---|---|
| Cancel the same appointment a second time | Refused: Cannot cancel an appointment that is already cancelled |
| Complete a cancelled appointment | Refused: Cannot complete an appointment that is already cancelled |
| Mark a cancelled appointment as a no-show | Refused: Cannot mark as a no-show an appointment that is already cancelled |
| Reschedule a cancelled appointment | Refused: Cannot reschedule an appointment that is already cancelled |
| Cancel a completed appointment | Refused: Cannot cancel an appointment that is already completed |
| Book a second appointment at a time the practitioner already holds | Refused: Dr. John Doe already has an appointment at 2024-07-23 09:00:00 |

### Reproducing these results

The domain layer produces no output on its own, so the checks above were run from a short script. Saving the code from Parts B, C and D as smartcare_v04.py and running the script beside it reproduces the results exactly.

```python
from smartcare_v04 import Appointment, DomainError, Patient, Practitioner

patient = Patient("Alice Smith", "0400 000 000")
practitioner = Practitioner("PR-001", "Dr. John Doe", "General Practice")
appointment = Appointment(patient, practitioner, "2024-07-20 10:00")
print("valid:", appointment.patient.name, appointment.practitioner.name,
      appointment.date_time, appointment.status.value)

for label, make in [
    ("empty patient name", lambda: Patient("", "0400 000 000")),
    ("patient name of spaces", lambda: Patient("   ", "0400 000 000")),
    ("practitioner with no identifier", lambda: Practitioner("", "Dr Roe", "GP")),
    ("no patient", lambda: Appointment(None, practitioner, "2024-07-21 09:00")),
    ("no practitioner", lambda: Appointment(patient, None, "2024-07-21 09:00")),
    ("no time", lambda: Appointment(patient, practitioner, "")),
    ("time that is not a time", lambda: Appointment(patient, practitioner, "tomorrow")),
    ("date with no time", lambda: Appointment(patient, practitioner, "2024-07-24")),
]:
    try:
        make()
        print(label, "accepted")
    except DomainError as error:
        print(label, "refused:", error)

appointment.cancel()
print("after cancel:", appointment.status.value,
      "| history:", len(patient.appointment_history()),
      "| slot free:", not practitioner.schedule().is_time_taken(appointment.date_time))

for label, attempt in [
    ("cancel twice", appointment.cancel),
    ("complete a cancelled appointment", appointment.complete),
    ("mark a cancelled appointment as a no-show", appointment.mark_no_show),
    ("reschedule a cancelled appointment",
     lambda: appointment.reschedule("2024-07-25 10:00")),
]:
    try:
        attempt()
        print(label, "allowed")
    except DomainError as error:
        print(label, "refused:", error)

completed = Appointment(patient, practitioner, "2024-07-22 11:00")
completed.complete()
try:
    completed.cancel()
except DomainError as error:
    print("cancel a completed appointment refused:", error)

Appointment(patient, practitioner, "2024-07-23 09:00")
try:
    Appointment(patient, practitioner, "2024-07-23 09:00")
except DomainError as error:
    print("double booking refused:", error)
```

Output:

```
valid: Alice Smith Dr. John Doe 2024-07-20 10:00:00 booked
empty patient name refused: Patient name must not be empty
patient name of spaces refused: Patient name must not be empty
practitioner with no identifier refused: Practitioner id must not be empty
no patient refused: An appointment needs a patient
no practitioner refused: An appointment needs a practitioner
no time refused: Appointment time must not be empty
time that is not a time refused: Appointment time must be a date and time, such as 2024-07-20 10:00
date with no time refused: Appointment time must include a time, such as 2024-07-20 10:00
after cancel: cancelled | history: 1 | slot free: True
cancel twice refused: Cannot cancel an appointment that is already cancelled
complete a cancelled appointment refused: Cannot complete an appointment that is already cancelled
mark a cancelled appointment as a no-show refused: Cannot mark as a no-show an appointment that is already cancelled
reschedule a cancelled appointment refused: Cannot reschedule an appointment that is already cancelled
cancel a completed appointment refused: Cannot cancel an appointment that is already completed
double booking refused: Dr. John Doe already has an appointment at 2024-07-23 09:00:00
```

## Part G - Refactor

The following changes were made to the generated Appointment class, and to the surrounding code, before arriving at the version shown in Parts B, C and D.

| Change | Reason |
|---|---|
| A single change_status() method became cancel(), complete() and mark_no_show() | One method taking any value leaves the decision with the caller, so US-04 cannot be enforced. |
| The appointment time is parsed into a date and time rather than kept as text | The approved UML already typed it as DateTime, so the code now matches it. FR-10 lists appointments in time order, which text cannot give. |
| is_time_taken() corrected so only a cancelled appointment frees its slot | A completed appointment still occupies the practitioner time, so its slot was wrongly being offered again. |
| A private registration helper became a public add_appointment() | Reaching into another class to attach an appointment was the fault the review rejected elsewhere. The method now refuses an appointment naming someone else. |
| schedule is reached through a method rather than a public attribute | The schedule is still stored on the practitioner. Providing it is a responsibility on the Practitioner CRC card, so it is exposed as an operation, and the updated UML shows it as one. |
| Attribute slots declared on all four classes | Instances cannot gain any attribute the class does not declare, so the stored state stays limited to those attributes. |

## Part H - AI Engineering Log

The prompt is recorded in Part D. Each generated contribution, the decision taken on it, the reason and the verification evidence are recorded in the AI Pair-Programming Record in Stage_4_SmartCare_v04_Domain_Implementation_Workbook.md.

| Decision | Count | Contribution |
|---|---|---|
| Accepted | 2 | The AppointmentStatus enum, and validation of patient, practitioner and time on creation |
| Modified | 1 | A single change_status(status) method |
| Rejected | 3 | Status as a public attribute, a NotificationManager call inside cancel(), and inheritance from a PatientRecord class |

## Reflection

The part I modified was the status handling. Copilot produced a single change_status(status) method with a public status attribute, which reads as reasonable code but leaves the decision with whoever calls it. US-04 says a completed appointment cannot be cancelled, and no method that accepts whatever value it is handed can enforce that. I replaced it with cancel(), complete() and mark_no_show(), each reading the current status before agreeing to change it, and made status readable but not writable.

I rejected three things outright. The public status attribute went for the reason above. A NotificationManager call inside cancel() went because reminders are still provisional and have never been confirmed by the client, so building it would commit the design to a delivery method nobody has agreed to. Inheritance from a PatientRecord class went because an appointment is not a kind of patient record, and inheriting would have copied a patient's details into every booking.

The approved design constrained the AI in a way I did not expect. Because the UML already named the classes, their attributes and the three relationships, anything the AI added beyond that stood out immediately. The inheritance from PatientRecord and the notification call were not judgement calls once the model was written down, because neither appears anywhere in it. Having the enum values fixed by FR-07 before the prompt was sent had the same effect: there was nothing to argue about, only something to check.
