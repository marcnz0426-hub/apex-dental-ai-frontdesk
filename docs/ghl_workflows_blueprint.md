# GoHighLevel Sub-Account & Workflows Blueprint

## 1. Practice Calendar (`Dr. Smith - General Dentistry`)
- **Timezone:** `America/New_York` (US Eastern)
- **Slot Duration & Interval:** 30 minutes (`:00` and `:30` grid)
- **Availability (Mon–Fri):**
  - Morning Block: `09:00 AM – 12:00 PM`
  - Blocked Lunch / Emergency Window: `12:00 PM – 01:00 PM` (Closed)
  - Afternoon Block: `01:00 PM – 05:00 PM`

## 2. Opportunity Pipeline (`Dental Patient Journey`)
1. `New Inbound Lead`
2. `Appointment Scheduled`
3. `Confirmed`
4. `Reactivation Queue`

## 3. Workflow 1: `Dental Booking Confirmation & Pipeline Update`
- **Trigger:** `Appointment Status` (Event type: `Normal`, Calendar: `Dr. Smith - General Dentistry`, Status: `Confirmed`)
- **Action 1:** `Create or update opportunity` -> Pipeline: `Dental Patient Journey`, Stage: `Appointment Scheduled`, Name: `{{contact.name}} - Checkup`
  - **Critical Setting:** Toggle **"Allow opportunity to move to any previous stage in pipeline"** = `ON` (allows reactivated contacts in Stage 4 to move cleanly back to Stage 2).
- **Action 2:** `Send SMS` -> Confirmation message with `{{contact.first_name}}` and `{{appointment.start_time}}`.

## 4. Workflow 2: `Patient Reactivation Campaign (SMS + Voice)`
- **Trigger:** `Contact Tag Added` = `reactivation-2025`
- **Action 1:** `Create or update opportunity` -> Pipeline: `Dental Patient Journey`, Stage: `Reactivation Queue`
- **Action 2:** `Send SMS` -> Reactivation offer with booking link and explicit `"Reply STOP to unsubscribe."`
- **Action 3:** `Wait` -> `2 Days`
- **Action 4:** `Custom Webhook` -> `POST https://api.retellai.com/v2/create-phone-call`
- **Workflow Settings:**
  - `Stop on response` = `ON` (terminates sequence immediately if patient replies or books).
  - TCPA Quiet Hours window: `9:00 AM – 6:00 PM` account timezone.
