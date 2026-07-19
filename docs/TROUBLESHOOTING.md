# Troubleshooting

## Bookings landing at the wrong time (multi-hour drift)

Retell sends naive datetime strings (no UTC offset) representing local time. If n8n's Google Calendar node falls back to the instance's default timezone instead of the caller's, bookings can land hours off (an 11 AM request landing at 8:30 PM was the original bug that led to this fix).

Fix: confirm `settings.timezone` on the workflow is explicitly pinned to `Asia/Kolkata` (or your local zone) — don't rely on the instance default.

## Retell never calls a function

- Confirm the Custom Function's URL in Retell matches the exact webhook path (`/webhook/retell-appointment-booking`, etc.) and that the workflow is **activated** in n8n, not just saved
- If testing locally via ngrok, check the tunnel is still running — ngrok URLs change on every restart unless you have a reserved domain, so a stale URL in Retell's Custom Function config is a common cause

## Cancel/Reschedule can't find the appointment

Both flows match by searching Calendar events tagged with the caller's phone number rather than a database. If lookups fail:
- Confirm the phone number format is consistent between the Book flow (what got written to the event) and what Retell passes on Cancel/Reschedule calls
- Check the search window in "Google Calendar - Search by Phone" / "Find Existing Appointment" covers the appointment's actual date

## Alternative slots come back empty or wrong

The slot-finding Code nodes (`Find 3 Closest Available Times`, `Calculate Available Slots`) depend on the working-hours/date-range config in the Workflow Configuration Set nodes. If these were changed, double check the search range still covers a reasonable number of days and the working hours match the actual business hours.

## Google Sheets logging fails

- Confirm `YOUR_GOOGLE_SHEET_ID` in "Append row in sheet" has been replaced with your real Sheet ID
- Confirm the Google Sheets OAuth2 credential has edit access to that sheet
