# Contributing

This is a personal automation project, but suggestions and fixes are welcome.

## Making changes

1. Export your modified workflow from n8n (Workflow menu -> Download)
2. Replace `ai-receptionist-appointments/AI-Receptionist-Appointments.json` with the new export
3. Before committing, scan the exported JSON for any raw credential values or API keys — n8n normally exports credential *references* only, but it's worth a quick check
4. Update `CHANGELOG.md` with a short note describing the change
5. Open a pull request with a clear description of what changed and why

## Reporting issues

Open an issue describing:
- Which flow was affected (Book / Cancel / Reschedule / Log)
- The exact error or unexpected behavior
- What the caller said vs. what Retell/n8n did in response, if relevant
