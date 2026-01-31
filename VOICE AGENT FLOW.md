# Voice Agent Flow Chart

```
                          Incoming Call
                               │
                               ▼
          "Are you a current tenant or looking for a rental?"
                               │
         ┌─────────────────────┴─────────────────────┐
         │                                           │
         ▼                                           ▼
   CURRENT TENANT                            PROSPECTIVE TENANT
   (no verification)                                 │
         │                                           ▼
         ▼                              "Looking for a new place, or
  "How can I help today?"                calling about an existing showing?"
         │                                           │
    ┌────┴────┐                          ┌───────────┴───────────┐
    │         │                          │                       │
    ▼         ▼                          ▼                       ▼
Maintenance  Other                 NEW INQUIRY             EXISTING SHOWING
    │      (Escalate)                    │                       │
    ▼         │                          ▼                       ▼
Gather:       │                    Qualify lead:           "When is it scheduled?"
• Issue       │                    • Bedrooms                    │
• Unit        │                    • Budget                      ▼
• Name        │                    • Move-in date          Lookup Appointment
• Phone       │                    • Pets                   (by time range)
• Email       │                    • Areas                       │
• Availability│                          │                 ┌─────┴─────┐
    │         │                          ▼                 │           │
    ▼         │                    Knowledge Base         Found    Not Found
Assess        │                       Lookup                │           │
Urgency       │                          │                  ▼           ▼
    │         │              ┌───────────┴────────┐   "Reschedule   "Check different
┌───┴───┐     │              │                    │    or cancel?"   week, or new
│       │     │              ▼                    ▼         │         showing?"
▼       ▼     │         Matches Found       No Matches      │
URGENT  NOT   │              │                    │    ┌────┴────┐
  │   URGENT  │              ▼                    │    │         │
  │     │     │      "Schedule showing?"          │ Reschedule Cancel
  ▼     │     │              │                    │    │         │
Create  │     │         ┌────┴────┐               │    ▼         ▼
Ticket  │     │         │         │               │  Check    Delete
(urgent)│     │        Yes        No              │  Avail    Appt
  │     │     │         │         │               │    │         │
  ▼     │     │         ▼         ▼               ▼    ▼         ▼
Send    │     │    Capture    Capture        Capture  Update   Confirm
SMS ────┼─────┼─→  Contact    Contact        Contact  Appt
(to     │     │         │         │               │    │
maint   │     │         ▼         │               │    │
team)   │     │    Check Avail    │               │    │
  │     │     │         │         │               │    │
  ▼     ▼     ▼         ▼         ▼               ▼    ▼
Confirm Create  Capture:   Book Appt      New Client CRM
        Ticket  • Name          │         (digits only for phone)
        (non-   • Phone         ▼               │
        urgent) • Email    New Client           │
          │     • Reason      CRM               │
          │         │           │               │
          ▼         ▼           ▼               ▼
       Confirm   Send       Confirm          Confirm
                 Email
                   │
                   ▼
                Confirm
```

## Tools Used

| Tool               | Purpose                                      |
| ------------------ | -------------------------------------------- |
| Create Ticket      | Maintenance requests (urgent/non-urgent)     |
| Send SMS           | Alert on-call maintenance team (urgent only) |
| Send Email         | Callback requests to property manager        |
| Check Availability | Find open calendar slots                     |
| Book Appointment   | Schedule showings                            |
| Lookup Appointment | Search by time range                         |
| Update Appointment | Reschedule showings                          |
| Delete Appointment | Cancel showings                              |
| New Client CRM     | Add prospect to CRM                          |
| Client Lookup      | Search CRM by phone number                   |

## Key Notes

- **Phone numbers**: Pass as digits only (e.g., 2145551234)
- **SMS**: Only for urgent maintenance → goes to maintenance team, not tenant
- **Appointment lookup**: By time range, not name/phone
- **Current tenants**: No verification needed
