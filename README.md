# AI Receptionist — Voice-Based Appointment Booking (n8n + Retell AI + Google Calendar)

A voice AI receptionist that handles a salon's appointment booking end-to-end over a phone call — no human needed for routine scheduling. Built as a four-flow n8n backend wired to a [Retell AI](https://www.retellai.com/) conversational voice agents.

## What it does

A caller talks to an AI voice agent ("Maya") that can:
- **Book** a new appointment, checking real-time availability against Google Calendar before confirming
- **Suggest alternatives** automatically if the requested slot is taken (finds the 3 closest open times)
- **Cancel** an existing appointment by looking it up via the caller's phone number
- **Reschedule** an existing appointment to a new time, with the same availability checks
- **Log** every booking to a Google Sheet for the business owner to review

## Architecture

```
Retell AI (voice agent, STT/TTS + LLM)
        │  Custom Functions (webhooks)
        ▼
n8n (self-hosted, Docker)
        │
        ├── Google Calendar API  →  availability checks, create/update/delete events
        └── Google Sheets API    →  appointment log
```

Retell's LLM decides *when* to call each function based on the conversation (e.g. it only calls `book_appointment` once it has collected the caller's name, service, and preferred time). Each function is a separate n8n webhook, so the four flows are fully independent and can be triggered, tested, and debugged in isolation.

## The four flows

| Flow | Webhook path | What it does |
|---|---|---|
| **Book** | `/webhook/retell-appointment-booking` | Checks calendar availability for the requested slot → books it if free, or returns the 3 closest available alternatives if not |
| **Cancel** | `/webhook/retell-cancel-appointment` | Searches the calendar for an upcoming event matching the caller's phone number and deletes it |
| **Reschedule** | `/webhook/reschedule-appointment` | Finds the caller's existing appointment, checks the new requested slot for conflicts, and updates the event |
| **Log** | `/webhook/log-appointment` | Appends a row (name, number, service, summary) to a Google Sheet after a successful booking |

## Key engineering details

- **Timezone handling**: Retell sends naive datetime strings (no UTC offset) that represent local time. n8n's Google Calendar node falls back to the *instance's* default timezone when no offset is present — so the workflow explicitly pins `settings.timezone` to `Asia/Kolkata` to avoid silent multi-hour drift (learned this the hard way: an 11 AM booking request landed at 8:30 PM before this fix).
- **Slot-finding algorithm**: a Code node scans working hours across a configurable date range (default: 10 days, 9 AM–5 PM, 30-min granularity) and ranks free slots by proximity to the caller's requested time, returning the top 3.
- **Idempotent lookups**: cancel/reschedule flows match appointments by searching the calendar for events tagged with the caller's phone number, rather than relying on a separate database.
- **Config isolation**: business rules (appointment duration, working hours, search range) are centralized in a single Set node per flow, so they can be tuned without touching the API-calling nodes.

## Stack

- **Voice/LLM orchestration**: Retell AI
- **Automation backend**: n8n (self-hosted via Docker)
- **Calendar**: Google Calendar API (OAuth2)
- **Logging**: Google Sheets API (OAuth2)
- **Tunneling (local dev)**: ngrok

## Setup

1. Import `AI-Receptionist-Appointments.json` into your n8n instance
2. Connect a **Google Calendar OAuth2** credential and a **Google Sheets OAuth2** credential to the relevant nodes
3. Replace `YOUR_GOOGLE_SHEET_ID` in the "Append row in sheet" node with your own sheet's ID
4. Activate the workflow, expose it publicly (ngrok for local dev, or a real domain in production)
5. In Retell, add four Custom Functions pointing at the four webhook paths above
6. Update your Retell agent's system prompt to call `log_appointment` after every successful booking

## Status

Working end-to-end in test conditions (Retell's Test LLM). Not yet connected to a live phone number — next step would be SIP trunking (e.g. via Twilio) for real inbound calls.

---

Built by [Samarth Mehrotra](https://github.com/Sam17meh) as part of ongoing work in AI/automation engineering.
