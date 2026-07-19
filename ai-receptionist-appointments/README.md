# Workflow: AI Receptionist Appointments

This folder contains the n8n workflow export for the appointment booking system.

See the [project README](../README.md) for full architecture, setup instructions, and engineering notes. This file just covers what's specific to the export itself.

## File

- `AI-Receptionist-Appointments.json` — the four flows (Book, Cancel, Reschedule, Log) as a single n8n workflow export

## Importing

Workflow menu -> Import from File -> select `AI-Receptionist-Appointments.json`.

After import, you'll need to reconnect the Google Calendar and Google Sheets OAuth2 credentials (these are never included in n8n exports) and set your own Sheet ID — see the main setup steps in the project README.
