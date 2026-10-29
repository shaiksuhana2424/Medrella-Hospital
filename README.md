# Medrella Hospital Appointment Booking System

Medrella is a single-hospital appointment booking application with patient, doctor, and hospital administrator portals. Patients and doctors can register with their own email address and password. Appointment data is shared: a confirmed booking appears in the patient’s appointments and the assigned doctor’s schedule.

## Run on Windows

1. Extract this ZIP to a folder such as `C:\Users\shaik\Desktop\Medrella-Hospital`.
2. Open that folder in File Explorer, click the address bar, type `cmd`, and press Enter.
3. Run `npm start`. No `npm install` is required.
4. Open `http://localhost:3000` in Chrome and keep the terminal open. Requires Node.js 20 or newer.

## Account setup and sign-in

### Patient registration
Choose **Create an account**, select **Patient**, enter your name and your own email address, and create a password of at least 8 characters. Use those same credentials whenever you sign in.

### Doctor registration
Choose **Create an account**, select **Doctor / clinician**, enter your name, your own email address, your own password, and medical specialty. After registration, Medrella creates an individual doctor profile and opens the doctor portal. Use those same personal credentials to sign in next time. Do not share accounts or passwords.

Doctor self-registration is enabled for this demo so it is easy to test. For a real hospital deployment, doctor registrations should be pending until hospital administration verifies professional credentials.

### Hospital administrator
The fresh local demo database includes a setup administrator account for managing the hospital directory:
- Email: `admin@medrella.demo`
- Password: `AdminMedrella123!`

This is only a local development bootstrap account, not a patient or doctor account. Change/remove the bootstrap account before any public deployment. The admin can view appointments and registered users and can add doctor accounts as well.

## Main workflows

- Patients book a doctor, choose an available date/time and scheduling method, see their appointment history, reschedule confirmed appointments, and cancel bookings.
- Doctors see appointments assigned to their own doctor profile and can reschedule or cancel confirmed visits and mark visits completed.
- Hospital administrators can view hospital-wide appointment activity, patient registrations, and doctors.
- **Stream scheduling:** one patient per doctor/date/time slot.
- **Wave scheduling:** up to three patients can share a doctor/date/time arrival window; wave slots cannot mix with stream bookings.
- **Rescheduling:** patients can reschedule their own confirmed appointments; doctors can reschedule appointments assigned to their own account. Availability and permissions are checked by the backend.
- Duplicate or unavailable bookings for the same doctor/date/time are rejected.
- Passwords are stored as `scrypt` hashes.

## Data and limitations

Local demo data is stored in `data/db.json`. This JSON-file store is suitable for learning/demo use, not real medical records or production traffic. Production use requires a managed database, persistent storage, HTTPS, stronger session management, audit logging, privacy controls, and verified clinician onboarding. `render.yaml` is a deployment template. If deployed on a free hosting plan, the service may sleep when idle and can take a short time to wake up.

## Checks

```bash
npm test
node --check server.js
node --check public/app.js
```

## Doctor consultation session management
Doctors can sign in to their doctor portal and create consultation sessions for a selected date, start/end time, and scheduling method. Sessions are stored in the shared data file and restrict patient booking times for that doctor/date to the configured session windows and method. Stream sessions allow one patient per time slot; wave sessions allow up to three patients per time slot. Dates without configured sessions retain the default appointment times for backward compatibility.

API endpoints (doctor authentication required):
- `GET /api/doctor/sessions` — list the signed-in doctor's sessions.
- `POST /api/doctor/sessions` — create a session with `date`, `startTime`, `endTime`, and `schedulingMode` (`STREAM` or `WAVE`).

## Doctor availability calendar

The patient booking form includes a seven-day doctor availability calendar. Select a doctor and scheduling method to see the dates with open slots, dates with no available slots, and configured consultation hours. Select a date card to update the appointment date and load its available times. The calendar uses the same availability endpoint and scheduling rules as appointment booking.
