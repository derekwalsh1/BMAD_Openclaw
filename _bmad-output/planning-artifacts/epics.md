# Epics & Stories: CoachConnect

**Version:** 1.0  
**Date:** 2026-02-22  
**Author:** Scrum Master Agent  
**Total Epics:** 4  
**Total Stories:** 18

## Overview

| Epic | Stories | Priority | Dependencies |
|------|---------|----------|--------------|
| Epic 1: Foundation & Auth | 5 | P0 | None |
| Epic 2: Client Management | 4 | P0 | Epic 1 |
| Epic 3: Session Scheduling & Notes | 5 | P0 | Epic 1, 2 |
| Epic 4: Payment Tracking | 4 | P0 | Epic 1, 2 |

---

## Epic 1: Foundation & Auth

**Goal:** Set up project infrastructure, UI foundation, and user authentication

**Value:** Enables coaches to securely access the platform and provides the base for all features

**Dependencies:** None

### Stories

#### Story 1.1: Project Setup & Configuration

**As a** developer  
**I want** to initialize the Next.js project with all dependencies  
**So that** I have a working development environment

**Acceptance Criteria:**

```gherkin
Given I am setting up the project
When I run the initialization commands
Then a Next.js 14+ project is created with TypeScript
And Supabase client is configured
And TailwindCSS and ShadCN UI are installed
And the project runs locally on localhost:3000

Given the project is initialized
When I check the codebase structure
Then I see organized directories: app/, components/, lib/, types/
And environment variables are documented in .env.example
And Git repository is initialized with .gitignore
```

**Technical Notes:**
- Use create-next-app with TypeScript flag
- Install: @supabase/ssr, @supabase/supabase-js, tailwindcss, lucide-react
- Configure ShadCN with `npx shadcn-ui@latest init`
- Create Supabase project and save credentials

**Dependencies:** None

**Estimated Effort:** S (4 hours)

---

#### Story 1.2: Database Schema & Supabase Setup

**As a** developer  
**I want** to create the complete database schema in Supabase  
**So that** all data can be stored and secured properly

**Acceptance Criteria:**

```gherkin
Given I have Supabase project credentials
When I execute the database migration
Then all tables are created: coaches, clients, sessions, packages, payments
And all foreign key relationships are established
And all indexes are created for performance
And Row Level Security (RLS) policies are enabled on all tables

Given RLS policies are active
When a coach queries clients table
Then they only see their own clients
And they cannot access other coaches' data
```

**Technical Notes:**
- Create SQL migration file
- Use UUID primary keys with gen_random_uuid()
- Create indexes on: coach_id, client_id, scheduled_at, status
- RLS policy: `coach_id = auth.uid()` for all tables
- Add database triggers for updated_at timestamps

**Dependencies:** Story 1.1

**Estimated Effort:** M (8 hours)

---

#### Story 1.3: Authentication Flow

**As a** volleyball coach  
**I want** to sign up and log in securely  
**So that** my client data is private and protected

**Acceptance Criteria:**

```gherkin
Given I am a new coach
When I visit /signup
Then I see a sign-up form with email and password fields
When I enter valid credentials and submit
Then my account is created in Supabase Auth
And a matching coach record is created in coaches table
And I am redirected to /dashboard

Given I have an account
When I visit /login
Then I see a login form
When I enter correct credentials
Then I am authenticated and redirected to /dashboard
And my session persists across page refreshes

Given I am logged in
When I click "Log out"
Then my session is cleared
And I am redirected to /login
```

**Technical Notes:**
- Use Supabase Auth (email/password)
- Create auth pages: app/(auth)/login/page.tsx, app/(auth)/signup/page.tsx
- Implement middleware for protected routes
- Use React Hook Form + Zod for validation
- Create database trigger to auto-create coach record on signup

**Dependencies:** Story 1.2

**Estimated Effort:** M (8 hours)

---

#### Story 1.4: Dashboard Layout & Navigation

**As a** coach  
**I want** a consistent navigation layout  
**So that** I can easily access all features

**Acceptance Criteria:**

```gherkin
Given I am logged in
When I view any dashboard page
Then I see a top navigation bar with the CoachConnect logo
And I see main navigation tabs: Dashboard, Clients, Schedule, Payments
And I see my name with a dropdown menu (Settings, Logout)
And the navigation is responsive on mobile devices

Given I am on a dashboard page
When I click a navigation tab
Then I am navigated to that section
And the active tab is highlighted
```

**Technical Notes:**
- Create layout: app/(dashboard)/layout.tsx
- Use ShadCN components: NavigationMenu, Avatar, DropdownMenu
- Implement mobile navigation (bottom bar or hamburger)
- Add active state styling
- Responsive: tabs on desktop, bottom nav on mobile

**Dependencies:** Story 1.3

**Estimated Effort:** M (6 hours)

---

#### Story 1.5: UI Component Library Setup

**As a** developer  
**I want** all core UI components ready  
**So that** I can build features quickly and consistently

**Acceptance Criteria:**

```gherkin
Given I need to build UI
When I import components from components/ui/
Then I have access to: Button, Card, Input, Label, Form, Badge, Dialog, Select
And all components follow the design system
And components are fully typed with TypeScript
And components are accessible (ARIA labels, keyboard nav)

Given I use form components
When I implement a form
Then validation works with Zod schemas
And error messages display correctly
```

**Technical Notes:**
- Install ShadCN components: button, card, input, label, form, badge, dialog, select, calendar, toast
- Configure theme colors in tailwind.config
- Create reusable form wrapper with react-hook-form
- Add toast notification system (Sonner or ShadCN toast)

**Dependencies:** Story 1.1

**Estimated Effort:** S (4 hours)

---

## Epic 2: Client Management

**Goal:** Enable coaches to add, view, edit, and manage their client roster

**Value:** Centralized client database replaces spreadsheets and notes

**Dependencies:** Epic 1

### Stories

#### Story 2.1: Client List View

**As a** coach  
**I want** to see all my clients in one place  
**So that** I can quickly find and access client information

**Acceptance Criteria:**

```gherkin
Given I am logged in
When I navigate to /clients
Then I see a list of all my active clients
And each client card shows: name, age, skill level, current package status, next session
And I see a "New Client" button
And I see a search input to filter clients

Given I have clients
When I type in the search box
Then the list filters in real-time by client name
And results update as I type

Given I have no clients
When I visit /clients
Then I see an empty state with "Add your first client" message
```

**Technical Notes:**
- Create page: app/(dashboard)/clients/page.tsx
- Fetch clients with Supabase query (coach_id filtered by RLS)
- Create ClientCard component
- Implement client-side search filter
- Use Server Components for data fetching
- Sort by: next session date, then name

**Dependencies:** Story 1.4, 1.5

**Estimated Effort:** M (6 hours)

---

#### Story 2.2: Add New Client

**As a** coach  
**I want** to add a new client with their information  
**So that** I can start tracking their sessions and progress

**Acceptance Criteria:**

```gherkin
Given I am on the clients page
When I click "New Client"
Then a modal/form opens with client fields
When I fill in: name (required), skill level (required), age, email, phone, goals
And I click "Create Client"
Then the client is saved to the database
And I am redirected to the new client's detail page
And I see a success message

Given I submit the form with missing required fields
When I click "Create Client"
Then validation errors are shown
And the form is not submitted
```

**Technical Notes:**
- Create ClientForm component (reusable for create/edit)
- Use Dialog or Modal component
- Validation: Zod schema (name required, email format, skill level enum)
- API route: POST /api/clients
- Optimistic UI update (add to list before server confirms)
- Option to create initial package during client creation

**Dependencies:** Story 2.1

**Estimated Effort:** M (8 hours)

---

#### Story 2.3: Client Detail Page

**As a** coach  
**I want** to view complete client information and history  
**So that** I can see their progress and upcoming sessions

**Acceptance Criteria:**

```gherkin
Given I have clients
When I click on a client from the list
Then I navigate to /clients/[id]
And I see the client's full profile (name, age, contact, skill level, goals)
And I see their current package with progress bar
And I see upcoming sessions for this client
And I see recent session history with notes
And I see an "Edit" button

Given the client has a low package balance (< 3 sessions)
When I view their detail page
Then I see a warning badge/alert about low balance
```

**Technical Notes:**
- Create page: app/(dashboard)/clients/[id]/page.tsx
- Fetch client data with related: current package, upcoming sessions, recent sessions
- Use tabs: Overview, Sessions, Packages (implement P1)
- Display package progress as progress bar
- Show session cards with collapsed notes (expand to view full)
- Add quick actions: Schedule Session, Add Payment

**Dependencies:** Story 2.1

**Estimated Effort:** L (10 hours)

---

#### Story 2.4: Edit Client Information

**As a** coach  
**I want** to update a client's information  
**So that** I can keep their profile current

**Acceptance Criteria:**

```gherkin
Given I am viewing a client detail page
When I click "Edit"
Then a form opens pre-filled with current client data
When I modify fields and click "Save"
Then the client record is updated in the database
And I see the updated information on the detail page
And I see a success message

Given I click "Edit" but then want to cancel
When I click "Cancel"
Then the form closes without saving
And no changes are made
```

**Technical Notes:**
- Reuse ClientForm component from Story 2.2
- Pre-populate form with client data
- API route: PATCH /api/clients/[id]
- Optimistic UI update
- Option to archive client (soft delete: archived = true)

**Dependencies:** Story 2.3

**Estimated Effort:** S (4 hours)

---

## Epic 3: Session Scheduling & Notes

**Goal:** Schedule sessions, track attendance, and document session notes

**Value:** Replaces texts and paper notes with organized digital tracking

**Dependencies:** Epic 1, Epic 2

### Stories

#### Story 3.1: Dashboard Home with Today's Sessions

**As a** coach  
**I want** to see today's schedule immediately when I log in  
**So that** I know who I'm coaching and when

**Acceptance Criteria:**

```gherkin
Given I am logged in
When I visit /dashboard
Then I see a greeting with today's date
And I see a "Today's Sessions" section
And I see all sessions scheduled for today with client names, times, and last session notes preview
And I see a "Quick Stats" section with: # sessions today, # active clients, # clients with low balance, revenue this week

Given I have no sessions today
When I view the dashboard
Then I see "No sessions scheduled today"
And I see a "Schedule Session" button
```

**Technical Notes:**
- Create page: app/(dashboard)/page.tsx (default route)
- Query sessions: WHERE DATE(scheduled_at) = CURRENT_DATE
- Display SessionCard component for each
- Add quick stats cards (count queries)
- "Start Session" button on each card → opens session modal
- Show previous session notes preview for context

**Dependencies:** Story 1.4, 2.1

**Estimated Effort:** M (8 hours)

---

#### Story 3.2: Schedule New Session

**As a** coach  
**I want** to schedule a session for a client  
**So that** it appears on my calendar

**Acceptance Criteria:**

```gherkin
Given I am on the dashboard or client detail page
When I click "New Session" or "Schedule Session"
Then a form opens with fields: client (dropdown), date, time, duration
When I select a client with an active package
And I choose a date and time
And I click "Schedule"
Then the session is created in the database
And it appears on the calendar
And the client's package sessions_used is NOT incremented (only when marked complete)

Given I try to schedule a session at a time when I already have one
When I submit the form
Then I see an error: "You already have a session at this time"
And the session is not created
```

**Technical Notes:**
- Create SessionForm component
- Client dropdown: only show clients with active packages
- Date/time picker: native or react-day-picker + time select
- Duration: default 60 min, options: 30, 60, 90
- API route: POST /api/sessions
- Validation: check for time conflicts (coach_id + scheduled_at overlap)
- Default status: 'scheduled'

**Dependencies:** Story 3.1

**Estimated Effort:** M (8 hours)

---

#### Story 3.3: Calendar View

**As a** coach  
**I want** to see all my sessions in a calendar  
**So that** I can visualize my schedule

**Acceptance Criteria:**

```gherkin
Given I have scheduled sessions
When I navigate to /schedule
Then I see a week view calendar with all sessions
And each session is color-coded by status (scheduled=blue, completed=green, canceled=gray)
And I can click < > to navigate weeks
And I can switch between Week and Month views

Given I click on a session in the calendar
When the session card appears
Then I can view/edit session details
```

**Technical Notes:**
- Create page: app/(dashboard)/schedule/page.tsx
- Use react-big-calendar or build custom with date-fns
- Fetch sessions for date range (current week/month)
- Color coding based on status
- Click session → open session detail modal
- Click empty slot → create new session (P1)

**Dependencies:** Story 3.2

**Estimated Effort:** L (10 hours)

---

#### Story 3.4: Complete Session & Add Notes

**As a** coach  
**I want** to mark a session complete and add notes  
**So that** I track what we worked on and athlete progress

**Acceptance Criteria:**

```gherkin
Given I have a scheduled session
When I click "Start Session" or open the session detail
Then I see a form with status dropdown, skills worked checkboxes, and note text areas
When I select status = "Completed"
And I check skills: Serving, Passing
And I add notes: "Great progress on float serve accuracy"
And I add next focus: "Work on jump serve approach"
And I click "Save & Mark Complete"
Then the session status is updated to 'completed'
And the notes are saved
And the client's package sessions_used is incremented by 1
And I see a success message
And I return to the dashboard

Given I mark a session as "No-show"
When I save
Then the package sessions_used is incremented
And the status is set to 'no-show'
```

**Technical Notes:**
- Create SessionDetailModal component
- Skills worked: checkbox tags (Serving, Passing, Setting, Hitting, Blocking, Defense)
- Notes fields: Feedback, Next Focus (text areas)
- Status options: Scheduled, Completed, No-show, Cancelled
- API route: PATCH /api/sessions/[id]
- On status = completed/no-show: increment package.sessions_used
- Show previous session notes for context
- Validation: notes required when marking complete

**Dependencies:** Story 3.2

**Estimated Effort:** L (10 hours)

---

#### Story 3.5: Session History for Client

**As a** coach  
**I want** to view all past sessions for a client  
**So that** I can review their progress over time

**Acceptance Criteria:**

```gherkin
Given I am viewing a client detail page
When I click the "Sessions" tab
Then I see a list of all sessions for this client (past and future)
And sessions are sorted by date (newest first)
And each session shows: date, status, skills worked, notes
And I can expand a session to see full notes

Given a client has many sessions
When I scroll the list
Then sessions load paginated (20 per page)
```

**Technical Notes:**
- Add Sessions tab to client detail page
- Query: sessions WHERE client_id ORDER BY scheduled_at DESC
- Group by status: Upcoming, Completed, Canceled
- Expandable session cards
- Show full notes, skills worked, timestamps

**Dependencies:** Story 2.3, 3.4

**Estimated Effort:** M (6 hours)

---

## Epic 4: Payment Tracking

**Goal:** Track session packages, payments, and balances

**Value:** No more lost revenue or forgotten payments

**Dependencies:** Epic 1, Epic 2

### Stories

#### Story 4.1: Create Session Package

**As a** coach  
**I want** to create a session package for a client  
**So that** I can track how many sessions they've purchased

**Acceptance Criteria:**

```gherkin
Given I am viewing a client detail page
When I click "New Package"
Then a form opens with fields: # of sessions, price
When I enter "10 sessions" and "$500"
And I click "Create Package"
Then a package is created in the database
And it becomes the client's active package
And sessions_used starts at 0

Given a client already has an active package
When I create a new package
Then the previous package is set to active=false
And the new package becomes active
```

**Technical Notes:**
- Create PackageForm component
- Fields: total_sessions (number), price (currency)
- API route: POST /api/packages
- Set active=true for new package, active=false for old ones
- Link to client_id and coach_id

**Dependencies:** Story 2.3

**Estimated Effort:** M (6 hours)

---

#### Story 4.2: Track Package Usage

**As a** coach  
**I want** package balances to automatically update when sessions are completed  
**So that** I always know how many sessions remain

**Acceptance Criteria:**

```gherkin
Given a client has a package with 10 sessions, 5 used
When their current package status is displayed
Then I see "5/10 sessions used, 5 remaining"
And I see a progress bar showing 50%

Given a session is marked complete or no-show
When the session is saved
Then the package.sessions_used increments by 1
And the remaining count updates

Given a package has 0 sessions remaining
When I try to schedule a new session
Then I see a warning: "Client has no sessions remaining"
But I can still schedule (coach discretion)
```

**Technical Notes:**
- Calculate remaining: total_sessions - sessions_used
- Update trigger or API logic: when session status → completed/no-show, increment
- Show low balance alert when remaining < 3
- Progress bar component with visual indicator

**Dependencies:** Story 4.1, 3.4

**Estimated Effort:** S (4 hours)

---

#### Story 4.3: Record Payment

**As a** coach  
**I want** to record when a client pays for a package  
**So that** I track revenue and who owes money

**Acceptance Criteria:**

```gherkin
Given a client has a package
When I click "Add Payment" on their detail page
Then a form opens with: amount, payment date, method (dropdown: Cash, Venmo, Zelle, Check)
When I enter "$500" paid today via Venmo
And I click "Record Payment"
Then a payment record is created linked to the client and package
And I see the payment in the client's payment history
And the package balance updates

Given a package costs $500 and I've recorded payments totaling $500
When I view the client detail
Then I see "Paid in Full" or balance = $0
```

**Technical Notes:**
- Create PaymentForm component
- Fields: amount, paid_at (date), payment_method (select)
- API route: POST /api/payments
- Link to client_id, package_id, coach_id
- Calculate balance: package.price - SUM(payments.amount)
- Display on client detail page

**Dependencies:** Story 4.1

**Estimated Effort:** M (8 hours)

---

#### Story 4.4: Payment Dashboard

**As a** coach  
**I want** to see all outstanding balances and recent payments  
**So that** I can follow up on unpaid packages

**Acceptance Criteria:**

```gherkin
Given I navigate to /payments
Then I see summary cards: revenue this week, revenue this month, # clients with balances due
And I see a list of clients with outstanding balances
And I see recent payment history

Given a client owes money on a package
When I view the outstanding balances list
Then I see: client name, package details, amount paid, amount owed
And I can click "Record Payment" to log payment

Given I want to see all payments ever
When I scroll the recent payments section
Then payments are paginated and I can load more
```

**Technical Notes:**
- Create page: app/(dashboard)/payments/page.tsx
- Summary stats: SUM(payments) grouped by week/month
- Outstanding balances query: packages JOIN payments, WHERE balance > 0
- Recent payments: ORDER BY paid_at DESC LIMIT 20
- Payment list component with filters (date range, client)

**Dependencies:** Story 4.3

**Estimated Effort:** M (8 hours)

---

## Story Dependency Graph

```
1.1 → 1.2 → 1.3 → 1.4 → 2.1 → 2.2 → 2.3 → 2.4
 │                  │      │              │
 └→ 1.5 ───────────┘      │              │
                           │              │
                           └─────────→ 3.1 → 3.2 → 3.3
                                     │              │
                                     └→ 3.4 ────────┤
                                            │       │
                                            └→ 3.5  │
                                                    │
                           ┌────────────────────────┘
                           │
                           └→ 4.1 → 4.2
                                 │    │
                                 └→ 4.3 → 4.4
```

## Requirement Traceability

### Epic 1: Foundation & Auth
- FR-AUTH-001, FR-AUTH-002, FR-AUTH-003 → Story 1.3
- NFR-DATA-001, NFR-DATA-002, NFR-DATA-004 → Story 1.2, 1.3
- NFR-UX-002 → Story 1.4

### Epic 2: Client Management
- FR-CLIENT-001, FR-CLIENT-002, FR-CLIENT-007 → Story 2.1, 2.2
- FR-CLIENT-003, FR-CLIENT-004 → Story 2.3, 2.4
- FR-CLIENT-006 → Story 2.1

### Epic 3: Session Scheduling & Notes
- FR-SCHED-001, FR-SCHED-002, FR-SCHED-005 → Story 3.2, 3.3
- FR-ATTEND-001, FR-ATTEND-003, FR-ATTEND-004, FR-ATTEND-006 → Story 3.4
- FR-ATTEND-005 → Story 3.5
- FR-DASH-001, FR-DASH-002 → Story 3.1

### Epic 4: Payment Tracking
- FR-PAY-001, FR-PAY-003, FR-PAY-004 → Story 4.1, 4.2
- FR-PAY-002, FR-PAY-006 → Story 4.3
- FR-PAY-005, FR-PAY-007 → Story 4.4

## Implementation Order Recommendation

**Sprint 1 (Foundation):**
- Story 1.1, 1.2, 1.3, 1.4, 1.5

**Sprint 2 (Clients):**
- Story 2.1, 2.2, 2.3, 2.4

**Sprint 3 (Sessions):**
- Story 3.1, 3.2, 3.4

**Sprint 4 (Calendar & Payments):**
- Story 3.3, 3.5, 4.1, 4.2

**Sprint 5 (Payments & Polish):**
- Story 4.3, 4.4, bug fixes, polish

**Estimated Total:** 10-12 weeks (2-2.5 weeks per sprint)

---

**Status:** Epics & Stories Complete  
**Total Story Points:** 18 stories (S=30hrs, M=48hrs, L=30hrs = ~108 hours)  
**Next:** Create Sprint Status Tracker
