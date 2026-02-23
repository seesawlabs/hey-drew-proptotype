# HeyDrew — User Flows

This document maps every major user journey in the application. Each flow shows who does what, in what order, and what happens at each step.

---

## Client Flows

### Flow 1: Onboarding (Token → Account → Profile)

This is how a new client gets into the system. Their CPA generates an access token and shares it (email, text, etc.).

```
Token Entry                Create Account          Business Profile
┌─────────────────┐       ┌──────────────────┐     ┌──────────────────┐
│ Enter access code│       │ Create password  │     │ Business name    │
│ (DREW-XXXX-XXXX)│──────▶│ (8+ chars, 1 upper│───▶│ Business type    │
│ Enter email      │       │  1 number)       │     │  - Retail/E-comm │
└─────────────────┘       │ Confirm password │     │  - Service       │
                          └──────────────────┘     │  - Consulting    │
                                                    │ Tax year         │
                                                    └────────┬─────────┘
                                                             │
Personal Profile            Profile Complete                 │
┌──────────────────┐       ┌──────────────────┐              │
│ Contact name     │       │ Summary of all   │              │
│ Phone number     │◀──────│ entered info     │◀─────────────┘
│ (auto-formatted) │       │ "Proceed to      │
│ Email            │       │  Dashboard"      │
└──────┬───────────┘       └──────────────────┘
       │
       ▼
   Dashboard
```

**Key details:**
- Token format: `DREW-XXXX-XXXX` (starts with "DREW", 14 characters total)
- Password requirements: 8+ characters, at least 1 uppercase letter, at least 1 number
- Phone auto-formats to `(XXX) XXX-XXXX`
- Business types: Retail/E-commerce, Service business, Consulting

---

### Flow 2: Conversational Strategy Discovery (Chat with Drew)

This is the core experience. Drew (the AI) asks questions about the client's business to surface relevant tax strategies. The conversation branches based on answers.

```
Chat Opens
    │
    ▼
"What type of business do you run?"
    ├── Retail / E-commerce
    ├── Service business
    └── Consulting
         │
         ▼
"Do you work from home or use part of your home for business?"
    ├── Yes, I work from home ─────────────────────┐
    └── No, separate office                         │
         │                                          │
         ▼                                          ▼
"Do you have employees          "Do you host business meetings
 or contractors?"                at your home?"
    ├── Yes, I have help             ├── Yes, sometimes ──────────┐
    └── Just me                      └── No, not really           │
         │                                │                       │
         ▼                                ▼                       ▼
"Upload your documents       "Upload your documents      ★ AUGUSTA RULE FOUND ★
 to find strategies"          to find strategies"        "Rent your home to your
                                                          business for up to 14
                                                          days/year — tax free!"
                                                              │
                                                    ┌─────────┴──────────┐
                                                    ▼                    ▼
                                              "Tell me more!"    "Upload docs
                                                    │             to verify"
                                                    ▼                 │
                                              Detailed Augusta        │
                                              Rule explanation        │
                                              + upload prompt         │
                                                    │                 │
                                                    └─────────┬───────┘
                                                              ▼
                                                        Upload Screen
```

**After document upload, a second discovery flow begins:**
```
"I've received your documents. Do you own your home?"
    ├── Yes ──▶ "Do you host business meetings at home?"
    │              ├── Yes ──▶ ★ Strategy cards shown (Augusta Rule, etc.)
    │              └── No  ──▶ Other strategies explored
    └── No (rent) ──▶ "Do you have employees?"
                        ├── Yes ──▶ Employee-related strategies
                        └── No  ──▶ Solo business strategies
```

**Side conversations available at any point:**
- "What documents do I need?" → List of recommended docs
- "How does the analysis work?" → 4-step process explanation
- "Is my data secure?" → Security assurances
- "I have questions" → FAQ-style responses

---

### Flow 3: Document Upload & Analysis

```
Upload Screen                    Scanning              Analyzing
┌──────────────────┐            ┌───────────────┐     ┌───────────────┐
│ Drag & drop zone │            │ Progress bar  │     │ "Analyzing    │
│ Camera capture   │───Submit──▶│ "Scanning     │────▶│  for strategy │
│ File list        │            │  documents…"  │     │  opportunities│
│                  │            └───────────────┘     │  …"           │
│ Accepted types:  │                                  └───────┬───────┘
│  - Tax returns   │                                          │
│  - Schedule C    │                                          ▼
│  - Expense recs  │                                  Strategies Found
│  - 1099 forms    │                                  ┌───────────────┐
└──────────────────┘                                  │ Strategy cards│
                                                      │ w/ savings    │
                                                      │ estimates     │
                                                      └───────────────┘
```

---

### Flow 4: Strategy Detail & Implementation

Once a strategy is found (e.g., Augusta Rule), the client can view details and track implementation.

```
Strategy Detail Screen
┌──────────────────────────────────────────┐
│ ★ Augusta Rule                           │
│ "Up to $24,000 in savings"               │
│                                          │
│ [How it works]                           │
│  1. Business pays YOU rent for meetings  │
│  2. Up to 14 days/year, income is untaxed│
│  3. Business deducts it as expense       │
│                                          │
│ [Implementation Tasks]                   │
│  ☑ Document home office space    Jan 31  │
│  ☑ Set rental rate (comparables) Jan 31  │
│  ☐ Update meeting minutes template Feb 1 │
│  ☐ Schedule Q1 board meeting     Mar 14  │
│  ☐ Invoice business for Q1 rent  Mar 19  │
│  ☐ Schedule Q2 board meeting     Jun 14  │
│  ☐ Invoice business for Q2 rent  Jun 19  │
│  ☐ ... (continues quarterly)             │
│                                          │
│ [Reminder toggle]                        │
└──────────────────────────────────────────┘
```

**Tasks have detail modals** — tapping a task shows:
- Full description of what to do
- Due date
- Completion checkbox

---

### Flow 5: Dashboard (Case Home)

The main hub after onboarding. Shows case status and provides navigation.

```
Dashboard
┌──────────────────────────────────────────┐
│ "Welcome, [First Name]!"                 │
│ [Business Name] · [Tax Year]             │
│                                          │
│ ┌──────────────────────────────────────┐ │
│ │ Case Card                            │ │
│ │ Status badge · Drew avatar           │ │
│ │ "Next task: [task name]"             │ │
│ └──────────────────────────────────────┘ │
│                                          │
│ Tax Prep Hub (unlocks after upload)      │
│ ┌──────────────────────────────────────┐ │
│ │ Document checklist with statuses     │ │
│ │ Progress bar                         │ │
│ │ Day tracker (Augusta Rule days used) │ │
│ └──────────────────────────────────────┘ │
│                                          │
│ Navigation:                              │
│  → Chat with Drew                        │
│  → Strategies                            │
│  → Messages                              │
│  → Settings                              │
└──────────────────────────────────────────┘
```

**Tax Prep Hub** includes:
- Document checklist (which docs are uploaded, missing, or pending)
- Day tracker for Augusta Rule (X of 14 days used, with calendar events)
- Progress bar for overall case completion

---

### Flow 6: Messaging

Clients can exchange messages with their assigned tax professional.

```
Inbox                        Thread
┌──────────────────────┐    ┌──────────────────────┐
│ Thread 1 (unread)    │    │ Back to Inbox        │
│ Thread 2             │───▶│                      │
│ Thread 3             │    │ [Message from CPA]   │
│                      │    │ [Message from client] │
│ Sender name          │    │ [Message from CPA]   │
│ Preview text         │    │                      │
│ Timestamp            │    │ [Reply input]        │
└──────────────────────┘    └──────────────────────┘
```

---

## Admin / Tax Professional Flows

### Flow 7: Case Queue & Review

```
Case Queue                              Case Detail
┌────────────────────────────────┐     ┌────────────────────────────────┐
│ Status filters:                │     │ Breadcrumb: Queue > Client     │
│ [All][New][In Review]          │     │ Status dropdown                │
│ [Needs Attention][Pending]     │     │ [Approve & Send] button        │
│ [Approved]                     │     │                                │
│                                │     │ Tabs:                          │
│ ┌────────────────────────────┐ │     │ [Overview][Documents][Strategies│
│ │ Case ID | Client | Status  │ │────▶│  ][Messages][Notes & Tasks]    │
│ │ Biz Type | Docs | Updated  │ │     │                                │
│ └────────────────────────────┘ │     │ Overview: Client info, business│
│ ┌────────────────────────────┐ │     │   details, case summary        │
│ │ ...                        │ │     │ Documents: Uploaded files with  │
│ └────────────────────────────┘ │     │   viewer, extracted data       │
└────────────────────────────────┘     │ Strategies: Approve/reject each│
                                       │ Messages: Thread with client   │
                                       │ Notes: Internal notes & tasks  │
                                       └────────────────────────────────┘
```

**Case statuses:** New → In Review → Needs Attention → Pending Client → Approved

---

### Flow 8: Strategy Library

Tax professionals can browse pre-built strategies and add them to client cases.

```
Strategy Library
┌────────────────────────────────┐
│ Search bar                     │
│                                │
│ ┌────────────────────────────┐ │
│ │ SEP-IRA Contributions      │ │
│ │ Save up to $66,000/year    │ │
│ │ [Add to Case]              │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ Self-Employed Health Ins.  │ │
│ │ Deduct 100% of premiums   │ │
│ │ [Add to Case]              │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ Hire Family Members        │ │
│ │ Save on payroll taxes      │ │
│ │ [Add to Case]              │ │
│ └────────────────────────────┘ │
│ ...                            │
└────────────────────────────────┘
```

---

### Flow 9: Access Token Management

CPAs generate invite tokens to onboard new clients.

```
Access Tokens
┌────────────────────────────────┐
│ [Generate & Send Token]        │
│                                │
│ Token List:                    │
│ ┌────────────────────────────┐ │
│ │ DREW-XXXX-XXXX | Active   │ │
│ │ Client: Unassigned         │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ DREW-YYYY-YYYY | Used     │ │
│ │ Client: Jane Smith         │ │
│ └────────────────────────────┘ │
└────────────────────────────────┘
```

---

### Flow 10: Activity Log

Audit trail of all actions across cases.

```
Activity Log
┌────────────────────────────────┐
│ Timeline:                      │
│ • Document uploaded - 2:30 PM  │
│ • Strategy approved - 1:15 PM  │
│ • Message sent - 12:00 PM      │
│ • Case status changed - 11:00 AM│
│ ...                            │
└────────────────────────────────┘
```
