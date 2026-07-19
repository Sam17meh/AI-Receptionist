# Changelog

## [Unreleased]
- Added .gitignore, LICENSE, .env.example
- De-duplicated subfolder README (now points to main project docs)
- Added docs/ (node reference, troubleshooting, flow diagram)

## Initial version
- Four independent n8n webhook flows: Book, Cancel, Reschedule, Log
- Google Calendar OAuth2 integration for availability checks and event CRUD
- Google Sheets OAuth2 integration for appointment logging
- Slot-finding algorithm ranking the 3 closest open times when a request slot is taken
- Timezone explicitly pinned to Asia/Kolkata to fix multi-hour drift on naive datetime strings from Retell
- Tested end-to-end via Retell's Test LLM (not yet connected to a live phone number)
