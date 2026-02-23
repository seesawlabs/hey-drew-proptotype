# HeyDrew — API & Backend Requirements

This document defines the backend services needed to turn the prototype into a production application. Grouped by domain.

> **Current state:** The prototype has zero backend. All data is simulated in JavaScript. This document specifies what needs to exist.

---

## Authentication & Access

### Token Redemption
```
POST /api/auth/redeem-token
```
- **Input:** `{ code: "DREW-XXXX-XXXX", email: "user@example.com" }`
- **Behavior:** Validates token format and existence, checks it hasn't been redeemed, associates email
- **Output:** `{ valid: true, token_id: "..." }` or error
- **Notes:** Does NOT create an account yet — just validates the token

### Account Creation
```
POST /api/auth/signup
```
- **Input:** `{ token_id: "...", email: "...", password: "..." }`
- **Behavior:** Creates client account, marks token as redeemed, returns session
- **Output:** `{ client_id: "...", session_token: "..." }`
- **Validation:** Password must be 8+ chars, 1 uppercase, 1 number

### Login
```
POST /api/auth/login
```
- **Input:** `{ email: "...", password: "..." }`
- **Output:** `{ session_token: "...", client_id: "..." }`

### Admin Login
```
POST /api/auth/admin/login
```
- Separate auth flow for tax professionals

---

## Client Profile

### Save Business Profile
```
POST /api/profile/business
```
- **Input:** `{ business_name, business_type, tax_year }`
- **Auth:** Client session required

### Save Personal Profile
```
POST /api/profile/personal
```
- **Input:** `{ contact_name, phone, email }`
- **Auth:** Client session required

### Get Profile
```
GET /api/profile
```
- **Output:** Combined business + personal profile data
- **Auth:** Client session required

---

## Cases

### Get Current Case
```
GET /api/case
```
- Returns the client's active case (status, assigned tax pro, next task, etc.)
- **Auth:** Client session required

### Get Case Queue (Admin)
```
GET /api/admin/cases
```
- **Query params:** `?status=new&sort=updated_at`
- Returns paginated list of all cases
- **Auth:** Admin session required

### Get Case Detail (Admin)
```
GET /api/admin/cases/:case_id
```
- Full case detail: client info, documents, strategies, messages, activity
- **Auth:** Admin session required

### Update Case Status (Admin)
```
PATCH /api/admin/cases/:case_id/status
```
- **Input:** `{ status: "in_review" }`
- **Valid transitions:** new → in_review → needs_attention → pending_client → approved
- **Auth:** Admin session required

### Approve & Send Case (Admin)
```
POST /api/admin/cases/:case_id/approve
```
- Marks case as approved, sends notification to client
- **Auth:** Admin session required

---

## Documents

### Upload Document
```
POST /api/documents/upload
```
- **Input:** Multipart form data (file + metadata)
- **Behavior:** Stores file, queues for analysis/parsing
- **Output:** `{ document_id, file_name, status: "pending" }`
- **Auth:** Client session required

### Get Documents (for a case)
```
GET /api/cases/:case_id/documents
```
- Returns list of documents with parse status and extracted data
- **Auth:** Client or Admin session

### Get Document Detail
```
GET /api/documents/:document_id
```
- Returns document metadata, parse status, extracted fields
- **Auth:** Client or Admin session

### Trigger Document Analysis
```
POST /api/cases/:case_id/analyze
```
- Kicks off AI analysis of uploaded documents
- Scans for strategy opportunities
- Creates StrategyRecommendation records
- **Auth:** Client session (triggered after upload) or Admin session

---

## Strategies

### Get Strategy Library
```
GET /api/strategies
```
- Returns all available strategy definitions
- **Query params:** `?search=augusta&category=home-based`
- **Auth:** Admin session required

### Get Recommended Strategies (for a case)
```
GET /api/cases/:case_id/strategies
```
- Returns strategies recommended for this specific case, with status
- **Auth:** Client or Admin session

### Approve Strategy (Admin)
```
POST /api/admin/cases/:case_id/strategies/:recommendation_id/approve
```
- **Auth:** Admin session required

### Reject Strategy (Admin)
```
POST /api/admin/cases/:case_id/strategies/:recommendation_id/reject
```
- **Auth:** Admin session required

### Add Strategy to Case (Admin)
```
POST /api/admin/cases/:case_id/strategies
```
- **Input:** `{ strategy_id: "..." }`
- Manually adds a strategy from the library to a case
- **Auth:** Admin session required

---

## Tasks

### Get Tasks (for a strategy recommendation)
```
GET /api/recommendations/:recommendation_id/tasks
```
- Returns ordered task list with completion status
- **Auth:** Client session

### Toggle Task Completion
```
PATCH /api/tasks/:task_id
```
- **Input:** `{ completed: true }` or `{ reminder_enabled: true }`
- **Auth:** Client session

### Get Next Task (Dashboard)
```
GET /api/case/next-task
```
- Returns the next incomplete task across all active strategies
- **Auth:** Client session

---

## Messaging

### Get Message Threads
```
GET /api/case/messages
```
- Returns threads with last message preview and unread count
- **Auth:** Client session

### Get Thread Messages
```
GET /api/messages/:thread_id
```
- Returns all messages in a thread, ordered by time
- **Auth:** Client or Admin session

### Send Message
```
POST /api/messages/:thread_id
```
- **Input:** `{ body: "..." }`
- **Auth:** Client or Admin session

### Get Messages for Case (Admin)
```
GET /api/admin/cases/:case_id/messages
```
- All threads and messages for a case
- **Auth:** Admin session

---

## Access Tokens (Admin)

### Generate Token
```
POST /api/admin/tokens
```
- **Input:** `{ client_email?: "..." }` (optional pre-assignment)
- **Output:** `{ code: "DREW-XXXX-XXXX", status: "active" }`
- **Auth:** Admin session required

### List Tokens
```
GET /api/admin/tokens
```
- Returns all tokens with status and assignment info
- **Auth:** Admin session required

---

## Activity Log (Admin)

### Get Activity Log
```
GET /api/admin/activity
```
- **Query params:** `?case_id=...&actor_type=client&limit=50`
- Returns chronological event feed
- **Auth:** Admin session required

---

## Chat / Conversational AI

### Send Chat Message
```
POST /api/chat/message
```
- **Input:** `{ case_id, message: "...", context: { step, previous_answers } }`
- **Output:** `{ response: "...", suggestions: [...], show_upload_card: false, show_strategy_card: false }`
- **Behavior:** AI processes message in context of conversation history, business profile, and uploaded documents. Returns response text plus UI hints (suggestion chips, cards to display).
- **Auth:** Client session required

### Get Chat History
```
GET /api/chat/history
```
- Returns full conversation for the current case
- **Auth:** Client session required

---

## Notifications (Future)

These aren't in the prototype but are implied by the settings screen:

```
POST /api/notifications/preferences
GET  /api/notifications/preferences
```
- Email/push notification preferences
- Strategy reminder notifications
- Case status change notifications
- New message notifications

---

## Summary: Endpoint Count

| Domain | Endpoints |
|---|---|
| Auth | 4 |
| Profile | 3 |
| Cases | 5 |
| Documents | 4 |
| Strategies | 5 |
| Tasks | 3 |
| Messaging | 4 |
| Tokens | 2 |
| Activity | 1 |
| Chat AI | 2 |
| **Total** | **33** |
