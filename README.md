# Atlas Property Group Voice Agent

Voice assistant for handling leasing and tenant support calls via VAPI + n8n.

## Files

| File                    | Purpose                                       |
| ----------------------- | --------------------------------------------- |
| `VOICE AGENT PROMPT.md` | Main VAPI prompt                              |
| `VOICE AGENT FLOW.md`   | ASCII flow chart                              |
| `KNOWLEDGE BASE.md`     | Company/property info for VAPI knowledge base |
| `n8n workflow/`         | n8n workflow JSON files                       |
| `data/tenants.csv`      | Sample tenant CRM data                        |
| `data/prospects.csv`    | Sample prospect/lead CRM data                 |

## CRM Data Schema

### Tenants (`data/tenants.csv`)

| Field             | Type     | Description                                    |
| ----------------- | -------- | ---------------------------------------------- |
| `ID`              | string   | Unique tenant ID (T-XXXXX)                     |
| `first_name`      | string   | Tenant first name                              |
| `last_name`       | string   | Tenant last name                               |
| `phone`           | string   | Contact phone                                  |
| `email`           | string   | Contact email                                  |
| `community`       | string   | Property/community name                        |
| `unit_number`     | string   | Unit or address                                |
| `lease_start`     | date     | Lease start date                               |
| `lease_end`       | date     | Lease end date                                 |
| `rent_amount`     | currency | Monthly rent                                   |
| `current_balance` | currency | Account balance (negative = credit)            |
| `next_due_date`   | date     | Next payment due                               |
| `status`          | enum     | `active`, `past_due`, `notice`                 |

### Prospects (`data/prospects.csv`)

| Field                   | Type     | Description                                    |
| ----------------------- | -------- | ---------------------------------------------- |
| `ID`                    | string   | Unique prospect ID or calendar event ID        |
| `Name`                  | string   | Full name                                      |
| `Phone`                 | string   | Contact phone                                  |
| `Email`                 | string   | Contact email                                  |
| `beds_requested`        | number   | Number of bedrooms needed                      |
| `max_budget`            | currency | Maximum monthly rent budget                    |
| `target_move_in`        | date     | Desired move-in date                           |
| `pets`                  | string   | Pet information                                |
| `preferred_communities` | string   | Comma-separated list of preferred communities  |
| `status`                | enum     | `new`, `showing_scheduled`, `toured`, `applied`, `waitlist` |
| `Date`                  | datetime | Record/showing date                            |
| `Notes`                 | string   | Additional notes                               |

## Setup

### Trello

Find your list IDs:

```
https://trello.com/b/[board_id].json
```

### Twilio

**Important:** Register A2P 10DLC before sending SMS in production.

- What is A2P 10DLC? https://help.twilio.com/articles/1260800720410-What-is-A2P-10DLC
- Registration guide: https://www.twilio.com/docs/messaging/compliance/a2p-10dlc

### n8n MCP

To test the MCP in Claude Code, update the .mcp.json file with the URL to your MCP server (copy from your browser) and add the secret key API from n8n.

## Tools

| Tool               | n8n Workflow                             |
| ------------------ | ---------------------------------------- |
| Create Ticket      | Creates Trello card for maintenance      |
| Send SMS           | Alerts on-call maintenance (urgent only) |
| Send Email         | Callback requests                        |
| Check Availability | Calendar lookup                          |
| Book Appointment   | Schedule showing                         |
| Lookup Appointment | Search by time range                     |
| Update Appointment | Reschedule                               |
| Delete Appointment | Cancel                                   |
| New Client CRM     | Add prospect to Google Sheets            |
| Client Lookup      | Search CRM by phone                      |
