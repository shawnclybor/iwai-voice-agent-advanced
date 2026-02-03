# Atlas Property Group Voice Agent

Voice assistant for handling leasing and tenant support calls via VAPI + n8n.

## Files

| File                    | Purpose                                       |
| ----------------------- | --------------------------------------------- |
| `VOICE AGENT PROMPT.md` | Main VAPI prompt                              |
| `VOICE AGENT FLOW.md`   | Conversation flow diagram                     |
| `KNOWLEDGE BASE.md`     | Company/property info for VAPI knowledge base |
| `n8n workflow/`         | n8n workflow JSON files                       |
| `data/`                 | Sample CRM data (tenants + prospects)         |

## Setup

### Step 1: Import n8n Workflows

1. In n8n, go to **Workflows** → **Add workflow** → **Import from file**
2. Import all JSON files from the `n8n workflow/` folder
3. Tag all imported workflows with "Vapi MCP" for organization

**Import order doesn't matter** — workflows reference each other by ID, which n8n resolves automatically.

### Step 2: Set Up n8n Credentials

Create the following credentials in n8n (**Settings** → **Credentials**):

| Credential | Used By | Setup |
|------------|---------|-------|
| **Trello API** | Create Ticket | [Trello API Key](https://trello.com/power-ups/admin) |
| **Twilio API** | Send SMS | Account SID + Auth Token from [Twilio Console](https://console.twilio.com) |
| **Gmail OAuth2** | Send Email, Create Ticket (urgent) | Connect via n8n's OAuth flow |
| **Google Calendar OAuth2** | Book/Update/Delete/Check Availability | Connect via n8n's OAuth flow |
| **Google Sheets OAuth2** | New Client CRM, Client Lookup, Book Appointment | Connect via n8n's OAuth flow |

After creating credentials, open each workflow and assign the appropriate credential to each node.

#### Configure Placeholders

Update these placeholders in the workflows:

**Trello (Create Ticket)**
- `YOUR_TRELLO_LIST_ID` — Get from `https://trello.com/b/[board_id].json`
- `YOUR_URGENT_LABEL_ID` — Red label ID from same JSON
- `YOUR_NON_URGENT_LABEL_ID` — Green label ID from same JSON

**Twilio (Send SMS)**
- `+15550000000` — Your Twilio phone number (From)
- `+15550000001` — On-call maintenance number (To)

**Google Sheets (CRM workflows)**
- `YOUR_GOOGLE_SHEET_ID` — Create a Google Sheet with columns matching `data/prospects.csv`

**Google Calendar**
- Select your calendar in the Calendar dropdown

### Step 3: Connect Claude Code to n8n MCP Server

1. In n8n, open **Vapi MCP Server** workflow
2. Click the **MCP Server Trigger** node
3. Copy the **Production URL** (looks like `https://your-instance.n8n.cloud/mcp/abc123`)
4. In n8n settings, create an API key or note the JWT token

5. Update `.mcp.json` in this repo:
```json
{
  "mcpServers": {
    "n8n": {
      "type": "http",
      "url": "https://your-n8n-instance.example.com/mcp/YOUR_WEBHOOK_ID_HERE",
      "headers": {
        "Authorization": "Bearer YOUR_JWT_TOKEN_HERE"
      }
    }
  }
}
```

6. Restart Claude Code to load the MCP server

### Step 4: Test and Verify MCP Tools

In Claude Code, test each tool:

```
# List available tools
"What tools do you have access to?"

# Test CRM lookup
"Look up the client with phone number 5552000001"

# Test calendar
"Check availability for next Monday"

# Test ticket creation (use test data)
"Create a non-urgent maintenance ticket for John Doe at Oakwood Commons unit 204, issue: leaky faucet, phone 5551000001, email john@example.com, available weekday mornings"
```

Verify in n8n:
- Check **Executions** tab for each workflow
- Confirm data appears in Trello/Google Sheets/Calendar

### Step 5: Build Agent in VAPI

1. Create a new assistant in [VAPI Dashboard](https://dashboard.vapi.ai)
2. **System Prompt**: Copy contents of `VOICE AGENT PROMPT.md`
3. **Knowledge Base**: Upload `KNOWLEDGE BASE.md`
4. **Tools**: Connect your n8n MCP server URL with authentication
5. **Voice**: Choose a voice (recommend: natural, professional)
6. **Phone Number**: Assign a VAPI phone number or import from Twilio

Test the agent using VAPI's built-in call simulator before going live.

---

## CRM Data Schema

### Tenants (`data/tenants.csv`)

| Field             | Type     | Description                         |
| ----------------- | -------- | ----------------------------------- |
| `ID`              | string   | Unique tenant ID (T-XXXXX)          |
| `first_name`      | string   | Tenant first name                   |
| `last_name`       | string   | Tenant last name                    |
| `phone`           | string   | Contact phone                       |
| `email`           | string   | Contact email                       |
| `community`       | string   | Property/community name             |
| `unit_number`     | string   | Unit or address                     |
| `lease_start`     | date     | Lease start date                    |
| `lease_end`       | date     | Lease end date                      |
| `rent_amount`     | currency | Monthly rent                        |
| `current_balance` | currency | Account balance (negative = credit) |
| `next_due_date`   | date     | Next payment due                    |
| `status`          | enum     | `active`, `past_due`, `notice`      |

### Prospects (`data/prospects.csv`)

| Field                   | Type     | Description                           |
| ----------------------- | -------- | ------------------------------------- |
| `ID`                    | string   | Unique prospect ID or calendar event ID |
| `Name`                  | string   | Full name                             |
| `Phone`                 | string   | Contact phone                         |
| `Email`                 | string   | Contact email                         |
| `beds_requested`        | number   | Number of bedrooms needed             |
| `max_budget`            | currency | Maximum monthly rent budget           |
| `target_move_in`        | date     | Desired move-in date                  |
| `pets`                  | string   | Pet information                       |
| `preferred_communities` | string   | Comma-separated list                  |
| `status`                | enum     | `new`, `showing_scheduled`, `toured`, `applied`, `waitlist` |
| `Date`                  | datetime | Record/showing date                   |
| `Notes`                 | string   | Additional notes                      |

---

## Compliance

### Twilio A2P 10DLC

**Required before production SMS.** Register your business and campaign:

- [What is A2P 10DLC?](https://help.twilio.com/articles/1260800720410-What-is-A2P-10DLC)
- [Registration Guide](https://www.twilio.com/docs/messaging/compliance/a2p-10dlc)
