# Node Reference

The workflow is one n8n file containing four independent webhook-triggered flows.

## Book flow (`/webhook/retell-appointment-booking`)

| Node | Purpose |
|---|---|
| Check & Book | Webhook trigger, receives caller name/service/requested time from Retell |
| Workflow Configuration | Set node centralizing business rules (appointment duration, working hours, search range) |
| Get availability in a calendar | Checks Google Calendar for conflicts at the requested time |
| Is Slot Available? | Branches based on whether the slot is free |
| Book Appointment | Creates the Google Calendar event if the slot is free |
| Get Events / Find 3 Closest Available Times | If the slot is taken, scans a configurable date range and ranks free slots by proximity to the requested time |
| Format Alternative Times Response | Formats the 3 alternatives for Retell to read back to the caller |
| Respond to Webhook / Return Response | Sends the result back to Retell |

## Cancel flow (`/webhook/retell-cancel-appointment`)

| Node | Purpose |
|---|---|
| Cancel | Webhook trigger, receives caller's phone number |
| Google Calendar - Search by Phone | Looks up an upcoming event tagged with that phone number |
| IF - Event Found? | Branches based on whether a matching appointment exists |
| Google Calendar - Cancel Event | Deletes the matched event |
| Respond - Cancelled OK / Respond - Not Found | Sends the outcome back to Retell |

## Reschedule flow (`/webhook/reschedule-appointment`)

| Node | Purpose |
|---|---|
| Reschedule | Webhook trigger, receives phone number + new requested time |
| Workflow Configuration1 | Business rules for this flow |
| Check Availability1 | Checks the new requested slot for conflicts |
| Find Existing Appointment | Locates the caller's current appointment by phone number |
| Appointment Found? | Branches based on lookup result |
| Update Appointment | Moves the event to the new time if available |
| Get Events in Range / Calculate Available Slots | Same alternative-slot logic as the Book flow, used if the new requested time is taken |
| Success Response / No Appointment Found Response / Alternative Slots Response | Sends the outcome back to Retell |

## Log flow (`/webhook/log-appointment`)

| Node | Purpose |
|---|---|
| Log | Webhook trigger, called by Retell after a successful booking |
| Append row in sheet | Writes name, phone number, service, and summary to the configured Google Sheet |
