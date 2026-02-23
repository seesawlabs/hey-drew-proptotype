# HeyDrew — Information Architecture

This document maps every screen in both applications, how they connect, and what's on each one.

---

## Client App — Screen Map

The client app renders inside a 390×844px phone frame. All screens are `<div class="page">` elements — only one is visible at a time. Navigation is handled by a `showScreen(screenId)` JavaScript function.

```
                        ┌──────────────┐
                        │ Token Entry  │
                        │ #token-screen│
                        └──────┬───────┘
                               │
                        ┌──────▼───────┐
                        │ Sign Up      │
                        │ #signup-screen│
                        └──────┬───────┘
                               │
                        ┌──────▼────────────┐
                        │ Business Profile   │
                        │ #business-profile  │
                        └──────┬─────────────┘
                               │
                        ┌──────▼────────────┐
                        │ Personal Profile   │
                        │ #personal-profile  │
                        └──────┬─────────────┘
                               │
                        ┌──────▼────────────┐
                        │ Profile Complete   │
                        │ #profile-complete  │
                        └──────┬─────────────┘
                               │
                    ┌──────────▼──────────┐
                    │     DASHBOARD       │
                    │   #case-home-screen │
                    └──┬──┬──┬──┬──┬──────┘
                       │  │  │  │  │
          ┌────────────┘  │  │  │  └────────────┐
          ▼               │  │  │               ▼
  ┌───────────────┐       │  │  │       ┌────────────┐
  │ Chat w/ Drew  │       │  │  │       │  Settings  │
  │ #chat-screen  │       │  │  │       │ #settings  │
  └───────┬───────┘       │  │  │       └────────────┘
          │               │  │  │
          ▼               │  │  └──────────────┐
  ┌───────────────┐       │  │                 ▼
  │ Upload Docs   │       │  │         ┌──────────────┐
  │ #upload-screen│       │  │         │  Messages    │
  └───────┬───────┘       │  │         │ #messages    │
          │               │  │         └──────┬───────┘
          ▼               │  │                │
  ┌───────────────┐       │  │         ┌──────▼───────┐
  │ Scanning      │       │  │         │ Thread 1/2/3 │
  │ #scanning     │       │  │         │ #msg-thread-N│
  └───────┬───────┘       │  │         └──────────────┘
          │               │  │
          ▼               │  │
  ┌───────────────┐       │  └──────────────────┐
  │ Analyzing     │       │                     ▼
  │ #analyzing    │       │             ┌───────────────┐
  └───────┬───────┘       │             │ Case Status   │
          │               │             │ #status-screen│
          ▼               │             └───────────────┘
  ┌────────────────┐      │
  │ Strategies     │      │
  │ Found          │      │
  │ #strategies-   │      │
  │  found-screen  │      │
  └───────┬────────┘      │
          │               │
          ▼               │
  ┌────────────────┐      │
  │ Strategy Detail│◀─────┘
  │ #strategy-     │  (also reachable from chat
  │  detail-screen │   and dashboard)
  └────────────────┘
```

### Screen Inventory

| Screen ID | Purpose | Entry Points |
|---|---|---|
| `#token-screen` | Enter access code + email | App launch |
| `#signup-screen` | Create password | After valid token |
| `#business-profile-screen` | Business name, type, tax year | After signup |
| `#personal-profile-screen` | Contact name, phone, email | After business profile |
| `#profile-complete-screen` | Review info summary | After personal profile |
| `#case-home-screen` | Main dashboard/hub | After onboarding; primary navigation |
| `#chat-screen` | Conversational AI interface | Dashboard |
| `#upload-screen` | Document upload (drag & drop + camera) | Chat, dashboard |
| `#scanning-screen` | Document scanning progress animation | After upload submit |
| `#analyzing-screen` | Analysis progress animation | After scanning |
| `#strategies-found-screen` | List of discovered strategies | After analysis |
| `#strategy-detail-screen` | Single strategy deep-dive + tasks | Strategy list, chat, dashboard |
| `#messages-screen` | Message inbox | Dashboard |
| `#message-thread-1/2/3` | Individual message conversations | Inbox |
| `#settings-screen` | Account & notification settings | Dashboard |
| `#status-screen` | Case status overview | Dashboard |

### Modals (Overlays)
| Modal | Purpose | Trigger |
|---|---|---|
| `#task-modal` | Strategy task detail (description, due date, checkbox) | Tapping a task item |
| `#tax-doc-modal` | Tax document detail with field-level data | Tapping a doc in Tax Prep Hub |
| Doc preview modal | Document preview sheet | Preview button on doc cards |

---

## Admin Dashboard — Screen Map

Desktop layout with a fixed sidebar navigation and scrollable main content area.

```
┌──────────┬──────────────────────────────────────┐
│          │                                      │
│ Sidebar  │  Main Content Area                   │
│          │                                      │
│ ┌──────┐ │  ┌──────────────────────────────────┐│
│ │Queue │─┼──│ #screen-queue                    ││
│ └──────┘ │  │ Filter bar + case table          ││
│ ┌──────┐ │  └──────────────────────────────────┘│
│ │Library│ │  ┌──────────────────────────────────┐│
│ └──────┘ │  │ #screen-case-detail              ││
│ ┌──────┐ │  │ Tabs: Overview | Docs | Strategies│
│ │Tokens│ │  │       | Messages | Notes & Tasks ││
│ └──────┘ │  └──────────────────────────────────┘│
│ ┌──────┐ │  ┌──────────────────────────────────┐│
│ │Activity││  │ #screen-library                 ││
│ └──────┘ │  │ Strategy cards with search       ││
│          │  └──────────────────────────────────┘│
│ ┌──────┐ │  ┌──────────────────────────────────┐│
│ │User  │ │  │ #screen-tokens                   ││
│ │Profile│ │  │ Token generator + token list     ││
│ └──────┘ │  └──────────────────────────────────┘│
│          │  ┌──────────────────────────────────┐│
│          │  │ #screen-activity                 ││
│          │  │ Audit timeline                   ││
│          │  └──────────────────────────────────┘│
└──────────┴──────────────────────────────────────┘
```

### Screen Inventory

| Screen ID | Purpose | Key Elements |
|---|---|---|
| `#screen-queue` | Case management queue | Filter buttons, sortable table, status badges, click-to-detail |
| `#screen-case-detail` | Individual case review | Status dropdown, approve button, 5-tab interface |
| `#screen-library` | Pre-built strategy catalog | Search, strategy cards with savings estimates |
| `#screen-tokens` | Client access token management | Generate button, token list with status |
| `#screen-activity` | Audit trail | Chronological event timeline |

### Case Detail Tabs

| Tab | Contains |
|---|---|
| Overview | Client info, business details, case summary |
| Documents | Uploaded files, document viewer, extracted data fields |
| Strategies | Recommended strategies with individual approve buttons |
| Messages | Threaded conversation with client |
| Notes & Tasks | Internal team notes, admin task tracking |

### Admin Modals & Toasts
- **Modal dialog** — Used for document viewer, add-strategy, and confirmations
- **Toast notifications** — Success/error feedback (auto-dismiss after 3 seconds)
