# Stage 3 From Requirements to Domain Models

## Candidate Concepts

| Candidate    | Class? | Reason                                                                                                                                                                   |
|--------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Patient      | Yes    | Has its own details and its own identity. FR-01 creates a patient record and FR-12 updates one, so a patient exists whether or not any appointment refers to it.         |
| Practitioner | Yes    | Has its own details and identity. FR-03 creates a practitioner record and FR-10 gives each practitioner a schedule of their own.                                         |
| Appointment  | Yes    | Joins a patient, a practitioner and a date and time, and its status changes over its life. FR-04, FR-07 and FR-08 all act on it.                                         |
| Name         | No     | A single piece of text belonging to a patient or a practitioner. It has no behaviour of its own, so it is an attribute rather than a class.                              |
| Clinic       | No     | The clinic runs from one site, so a Clinic class would hold every other object without doing any work of its own. No requirement refers to a second site.                |
| Database     | No     | A storage technology rather than something that exists in the clinic's world. NFR-01 requires data to survive a restart, but how that is done is still an open question. |
| Cancellation | No     | Cancelling is something done to an appointment. FR-07 records the outcome as a status change, not as a separate record.                                                  |
| Status       | No     | One of a fixed set of values held by an appointment: booked, completed, cancelled or no-show. It is an attribute of Appointment.                                         |

## CRC Cards

### Patient

| Responsibilities                                                                       | Collaborators |
|----------------------------------------------------------------------------------------|---------------|
| Know its own name and contact details, and update them when they change (FR-01, FR-12) | None          |
| Provide its history of past appointments (FR-09)                                       | Appointment   |

### Practitioner

| Responsibilities                                      | Collaborators |
|-------------------------------------------------------|---------------|
| Know its own name and specialty (FR-03)               | None          |
| Provide its schedule of upcoming appointments (FR-10) | Appointment   |

### Appointment

| Responsibilities                                                  | Collaborators         |
|-------------------------------------------------------------------|-----------------------|
| Know its patient, its practitioner, and its date and time (FR-04) | Patient, Practitioner |
| Know its own status and change it when asked (FR-07)              | None                  |

## Relationship Reasoning

**1. Patient to Appointment: which relationship and why?**

**Relationship:** One to many. (One patient to many appointments.)

**Why?**

FR-09 requires a history of past appointments to be kept for each patient,
which only makes sense if one patient can hold many appointments. At the other
end, FR-04 books an appointment for one named patient, so each appointment
belongs to exactly one patient.

**2. Practitioner to Appointment: what multiplicity?**

**Multiplicity:** One to zero or many. (1 to 0..\*)

**Why?**

FR-04 books a patient with a specific practitioner, so each appointment has
exactly one. A practitioner can hold many appointments, and may have none
booked at all, which is why the lower bound is zero. FR-10 depends on that: an
empty schedule should display as empty rather than fail.

**3. Should Appointment inherit from Patient?**

**Answer:** No. Appointment should not inherit from Patient.

**Why?**

Inheritance means one thing is a kind of another, and an appointment is not a
kind of patient. It refers to one. Inheriting would also give every
appointment its own copy of a patient's contact details, so updating a phone
number under FR-12 would mean editing every appointment that patient has had.

**4. Does Clinic need to own every object?**

**Answer:** No. Clinic does not need to own every object.

**Why?**

The clinic runs from one site, so a Clinic holding every patient, practitioner
and appointment would add a step to every operation without making anything
possible that is not possible already. No requirement asks for it.

## AI Model Critique

### 1. PatientManager

**AI proposal:** a separate class that creates, searches and updates patient
records.

**What is good:**

- FR-02 searches across all patient records, and no single Patient can search
  the others, so something does have to hold the collection.

**Problems:**

- Creating and updating a record (FR-01, FR-12) is work Patient does for
  itself. Moving it out leaves Patient as data with no behavior.
- Holding a collection and searching it is one operation, which is too small to
  justify a class of its own.

**Suggested improvement:** drop the class. Keep FR-01 and FR-12 on Patient and
treat the search as an operation over the collection of patients.

### 2. PractitionerManager

**AI proposal:** the same pattern applied to practitioner records.

**What is good:**

- The same collection argument would apply if practitioner records ever had to
  be searched across.

**Problems:**

- No requirement searches practitioners. FR-03 creates one and FR-10 reads a
  single practitioner's own schedule, so nothing spans them all.
- FR-03 and FR-10 are already met by Practitioner itself.

**Suggested improvement:** drop the class. Unlike PatientManager, not even the
collection argument applies here.

### 3. AppointmentManager

**AI proposal:** a class that books, cancels and reschedules appointments and
checks for clashes.

**What is good:**

- FR-05 genuinely needs something that can see all of a practitioner's
  appointments. A single appointment cannot tell whether it clashes with
  another, so this is a real gap the proposal spotted.
- NFR-03 backs this up. A conflicting booking has to be blocked in the data
  rather than only warned about on screen, so the check does belong in a class.

**Problems:**

- Booking, cancelling and rescheduling (FR-04, FR-07, FR-08) each act on one
  appointment and belong to Appointment.
- Bundling conflict checking together with all of that makes the class a
  catch-all, and the name says nothing about what it actually holds.

**Suggested improvement:** keep only the conflict-checking responsibility and
give it to a class that holds one practitioner's appointments. Leave FR-04,
FR-07 and FR-08 with Appointment.

### 4. ClinicController

**AI proposal:** a top-level class owning all patients, practitioners and
appointments, with every operation routed through it.

**What is good:**

- Would give one obvious entry point if a user interface were added later.

**Problems:**

- No requirement describes a clinic-level controller, and the clinic runs from
  a single site, so there is nothing for it to distinguish between.
- Every operation passing through it creates a second place to change whenever
  any class changes.

**Suggested improvement:** leave it out until the client confirms either more
than one site or an interface that needs a single entry point.

### 5. NotificationManager

**AI proposal:** a class that sends appointment reminders by SMS or email.

**What is good:**

- If reminders were confirmed, separating message sending from the domain
  classes would be the right shape.

**Problems:**

- Reminders are still provisional and have not been confirmed by the client.
- No functional requirement covers them. Building it now would commit the
  design to a delivery method and an external service nobody has agreed to.

**Suggested improvement:** leave it out and keep reminders on the provisional
list until the client confirms them.

### 6. ScheduleEngine

**AI proposal:** a class that works out available slots and allocates
appointment times.

**What is good:**

- Names a real question the requirements do not answer: how free time is
  worked out.

**Problems:**

- FR-10 asks a practitioner to view their own appointments, which means
  reading records that already exist.
- Nothing asks the system to generate or suggest times, so "Engine" implies
  allocation logic no requirement describes.

**Suggested improvement:** drop the allocation idea. Viewing a schedule is
satisfied by listing that practitioner's appointments in time order.
