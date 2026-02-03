# CLAUDE.md

This file provides guidance to when working in this repository.

## Project Overview

Voice assistant system for Atlas Property Group (VAPI + n8n). The agent "Dagny" handles leasing inquiries and tenant maintenance requests. See `README.md` for setup instructions.

## Architecture

```
Incoming Call (VAPI)
        │
        ▼
┌───────────────────┐
│  Voice Agent      │  ← VOICE AGENT PROMPT.md (system prompt)
│  (VAPI)           │  ← KNOWLEDGE BASE.md (property info)
└───────────────────┘
        │
        ▼ MCP Protocol
┌───────────────────┐
│  n8n MCP Server   │  ← Vapi MCP Server.json
└───────────────────┘
        │
        ▼
┌─────────────────────────────────────────┐
│  n8n Workflows → External Services      │
│  • Create Ticket → Trello               │
│  • Send SMS → Twilio                    │
│  • Send Email → Gmail                   │
│  • Calendar ops → Google Calendar       │
│  • CRM ops → Google Sheets              │
└─────────────────────────────────────────┘
```

## Tool Data Formats

**Phone numbers**: Digits only (`2145551234` not `214-555-1234`) for CRM tools.

**Appointment operations**: Lookup by time range (not name/phone). Must call `Lookup Appointment` before `Update Appointment` or `Delete Appointment`.

**Maintenance urgency**: String values `"urgent"` or `"non-urgent"`. Urgent triggers SMS to on-call team.

## Conversation Flow

Two main paths from `VOICE AGENT FLOW.md`:

1. **Current Tenant** → Maintenance request or escalate to callback
2. **Prospective Tenant** → Qualify lead → Schedule showing or add to CRM

Urgent maintenance criteria (from prompt):

- Water leak/flooding
- No heat (<50°F) or AC (>90°F)
- Gas smell
- Electrical hazard
- Security issues (locks, windows)

## n8n Workflow Dependencies

The MCP Server workflow (`Vapi MCP Server.json`) routes to child workflows:

| MCP Tool           | Child Workflow     | External Service         |
| ------------------ | ------------------ | ------------------------ |
| Create Ticket      | Create Ticket      | Trello                   |
| Send SMS           | Send SMS           | Twilio                   |
| Send Email         | Send Email         | Gmail                    |
| Check Availability | Check Availability | Google Calendar          |
| Book Appointment   | Book Appointment   | Google Calendar + Sheets |
| Update Appointment | Update Appointment | Google Calendar          |
| Delete Appointment | Delete Appointment | Google Calendar          |
| Lookup Appointment | Lookup Appointment | Google Calendar          |
| New Client CRM     | New Client CRM     | Google Sheets            |
| Client Lookup      | Client Lookup CRM  | Google Sheets            |
