# Engineering Troubleshooting & Failure Mode Log

> **System:** Apex Dental AI Front-Desk & Patient Reactivation Engine  
> **Stack:** Retell AI + n8n Cloud + GoHighLevel V2 API

---

## Case Study 01: n8n Respond to Webhook Mixed Expression AST Crash (HTTP 500)

- **Incident:** Execution `#52` and `#53` failed with `HTTP 500: Invalid JSON in 'Response Body' field` at the `Respond Blocked` node.
- **Root Cause:** Mixed expression syntax (`=` JS prefix combined with Handlebars `{{ JSON.stringify() }}` and unquoted `+` concatenation) broke n8n's V2 AST parser.
- **Resolution:** Refactored both `Respond to Webhook` nodes into unified JavaScript Object Expressions (`={{ { ... } }}`), resolving execution latency down to `76ms` (`ID#54` / `ID#55`).

---

## Case Study 02: GoHighLevel V2 Least-Privilege Scopes (HTTP 401) & 30-Min Interval Validation (HTTP 400)

- **Incident 1 (`HTTP 401`):** Initial Private Integration testing returned `The token is not authorized for this scope` on Calendar endpoints because `Calendar Groups / Resources` scopes were enabled instead of core `Calendars` and `Calendar Events`.
  - **Resolution:** Updated scopes strictly to `Contacts (R/W)`, `Calendars (R/W)`, and `Calendar Events (R/W)`.
- **Incident 2 (`HTTP 400`):** Testing `3:15 PM` (`15:15:00-05:00`) triggered `400 Bad Request: The slot you have selected is no longer available` on a 30-minute interval calendar.
  - **Resolution:** Implemented a **30-Minute Boundary Snapping Normalizer** in the n8n Code node (`:00` / `:30` alignment) and updated alternate slot suggestions (`2:30 PM`, `3:30 PM`). Verified live `201 Created` booking sync (`ID#64` / `ID#66`).
