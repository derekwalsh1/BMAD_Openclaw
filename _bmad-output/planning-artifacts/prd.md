# Product Requirements Document: CoachConnect

**Version:** 1.0  
**Date:** 2026-02-22  
**Author:** Business Analyst Agent  
**Product:** CoachConnect - Volleyball Private Lesson Management

## 1. Overview

CoachConnect helps volleyball coaches manage their private lesson business by centralizing client data, scheduling, attendance, progress tracking, and payment management in one web application.

## 2. User Personas

### Persona 1: Derek - The Busy Coach
- **Age:** 30-45
- **Experience:** 5-15 years coaching
- **Clients:** 20-30 active athletes
- **Sessions:** 15-20 per week
- **Pain Points:** 
  - Spends 2hrs/day on admin tasks
  - Loses track of who paid what
  - Can't remember what was covered in last session
  - Double-books sessions occasionally
- **Goals:**
  - More time coaching, less admin
  - Professional client experience
  - Track athlete development
  - Get paid on time

### Persona 2: Sarah - The Parent/Athlete
- **Age:** 14 (athlete) + parent (40s)
- **Level:** High school competitive
- **Frequency:** 2 sessions/week
- **Pain Points:**
  - Unsure what progress is being made
  - Forgets session times
  - Doesn't know package balance
- **Goals:**
  - See measurable progress
  - Easy scheduling
  - Clear payment tracking

## 3. User Journeys

### Journey 1: Onboarding a New Client

**Actor:** Coach  
**Frequency:** 2-3 times per month

1. Coach receives inquiry from new client
2. Logs into CoachConnect
3. Clicks "Add New Client"
4. Enters athlete information:
   - Name, age, contact
   - Skill level (beginner/intermediate/advanced)
   - Position preferences
   - Goals
5. Creates initial session package (e.g., "4 sessions for $200")
6. Schedules first session
7. Sends welcome info to client (manual for MVP)

**Success Criteria:** Client profile created in < 2 minutes

### Journey 2: Weekly Scheduling & Prep

**Actor:** Coach  
**Frequency:** Weekly

1. Coach logs in Monday morning
2. Views weekly calendar
3. Sees upcoming sessions with client names
4. Reviews previous session notes for each client
5. Plans what to work on based on progress
6. Checks which clients have low package balances

**Success Criteria:** Weekly prep in < 15 minutes

### Journey 3: Conducting & Documenting a Session

**Actor:** Coach  
**Frequency:** 15-20 times per week

1. Coach arrives at court
2. Conducts 60-minute private lesson
3. After session, opens CoachConnect on phone
4. Finds today's session
5. Marks session as "Complete"
6. Adds quick notes:
   - Skills worked (serving, passing, hitting)
   - Key wins
   - Areas for next session
   - Homework/practice recommendations
7. Saves session
8. System auto-deducts from client's package balance

**Success Criteria:** Session documentation in < 3 minutes

### Journey 4: End of Month Payment Review

**Actor:** Coach  
**Frequency:** Monthly

1. Coach opens payment dashboard
2. Views clients with outstanding balances
3. Sees who needs to renew packages
4. Sends payment reminders (manual for MVP)
5. Marks payments as received when paid
6. Reviews monthly revenue total

**Success Criteria:** Payment review in < 20 minutes

### Journey 5: Parent Checking Progress

**Actor:** Parent (Future - V2)  
**Frequency:** Weekly  
**Note:** V1 = coach shares manually, V2 = client portal

1. Parent receives monthly progress summary from coach
2. Reviews skills worked
3. Sees attendance record
4. Checks package balance

## 4. Functional Requirements

### FR-AUTH: Authentication & Access

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-AUTH-001 | Coach can create account with email/password | P0 | Account created, email verified |
| FR-AUTH-002 | Coach can log in | P0 | Successful login, session persists |
| FR-AUTH-003 | Coach can log out | P0 | Session cleared |
| FR-AUTH-004 | Coach can reset password | P1 | Reset email sent, password updated |

### FR-CLIENT: Client Management

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-CLIENT-001 | Coach can add new client | P0 | Client profile created with required fields |
| FR-CLIENT-002 | Coach can view all clients | P0 | List shows all active clients |
| FR-CLIENT-003 | Coach can view client detail | P0 | Full profile, session history, notes |
| FR-CLIENT-004 | Coach can edit client info | P0 | Changes saved successfully |
| FR-CLIENT-005 | Coach can archive client | P1 | Client hidden from active list |
| FR-CLIENT-006 | Coach can search clients | P1 | Search by name works |
| FR-CLIENT-007 | Client profile includes: name, age, contact, skill level, goals | P0 | All fields editable |

### FR-SCHEDULE: Session Scheduling

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-SCHED-001 | Coach can create session for client | P0 | Session created with date/time/client |
| FR-SCHED-002 | Coach can view calendar (week/month) | P0 | All sessions displayed correctly |
| FR-SCHED-003 | Coach can edit session time | P0 | Time updated, no conflicts |
| FR-SCHED-004 | Coach can cancel session | P0 | Session marked cancelled |
| FR-SCHED-005 | System prevents double-booking | P0 | Error shown on time conflict |
| FR-SCHED-006 | Coach can set recurring sessions | P1 | Weekly sessions auto-created |
| FR-SCHED-007 | Calendar shows today/upcoming sessions on dashboard | P0 | Correct sessions displayed |

### FR-ATTEND: Attendance & Session Notes

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-ATTEND-001 | Coach can mark session complete | P0 | Status = complete, timestamp saved |
| FR-ATTEND-002 | Coach can mark session no-show | P0 | Status = no-show, package deducted |
| FR-ATTEND-003 | Coach can add session notes | P0 | Notes saved, viewable later |
| FR-ATTEND-004 | Session notes include: skills worked, feedback, next steps | P0 | All fields available |
| FR-ATTEND-005 | Coach can view session history for client | P0 | All past sessions with notes |
| FR-ATTEND-006 | Completing session auto-deducts from package | P0 | Balance updated correctly |

### FR-PAYMENT: Payment & Package Management

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-PAY-001 | Coach can create session package for client | P0 | Package created (sessions, price) |
| FR-PAY-002 | Coach can record payment received | P0 | Payment logged, balance updated |
| FR-PAY-003 | System tracks remaining sessions in package | P0 | Count accurate after sessions |
| FR-PAY-004 | Coach can view client balance | P0 | Current package status visible |
| FR-PAY-005 | Coach can view all outstanding balances | P0 | Payment dashboard shows totals |
| FR-PAY-006 | Coach can view payment history for client | P1 | All transactions listed |
| FR-PAY-007 | System alerts when package running low (< 2 sessions) | P1 | Alert shown on dashboard |

### FR-DASH: Dashboard & Reports

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-DASH-001 | Coach sees today's sessions on dashboard | P0 | All today's sessions listed |
| FR-DASH-002 | Coach sees this week's upcoming sessions | P0 | Chronological list |
| FR-DASH-003 | Coach sees clients with low balances | P0 | List of clients < 2 sessions |
| FR-DASH-004 | Coach sees recent activity feed | P1 | Last 10 sessions, payments |
| FR-DASH-005 | Coach sees monthly revenue total | P1 | Sum of payments this month |
| FR-DASH-006 | Coach sees total # active clients | P1 | Count displayed |

## 5. Non-Functional Requirements

### NFR-PERF: Performance
- **NFR-PERF-001:** Pages load in < 2 seconds
- **NFR-PERF-002:** Works on mobile browsers (responsive)

### NFR-DATA: Data & Security
- **NFR-DATA-001:** Client data encrypted at rest
- **NFR-DATA-002:** HTTPS required for all connections
- **NFR-DATA-003:** Daily automated backups
- **NFR-DATA-004:** Coach can only see own clients (multi-tenancy)

### NFR-UX: User Experience
- **NFR-UX-001:** Mobile-friendly interface (coach uses phone)
- **NFR-UX-002:** Intuitive navigation, < 3 clicks to any feature
- **NFR-UX-003:** Accessible color contrast, readable fonts

### NFR-SCALE: Scalability
- **NFR-SCALE-001:** Support 1000+ coaches on platform
- **NFR-SCALE-002:** Support coaches with up to 100 clients

## 6. Dependencies & Assumptions

### Dependencies
- None (MVP is self-contained)

### Assumptions
- Coach has smartphone or laptop
- Coach has internet access
- Coach manually handles payment collection (Venmo, cash, etc.)
- One coach per account (no team coaching)

## 7. Out of Scope (V1)

- Client self-service portal
- Automated email/SMS reminders
- Payment processing (Stripe integration)
- Video upload/analysis
- Calendar integrations (Google Calendar sync)
- Multi-coach accounts
- Mobile native apps
- Public booking page
- Contract/waiver management

## 8. Success Criteria

### Business Metrics
- 80% reduction in coach admin time
- 100% of session notes captured
- 0 double-booked sessions
- 15% improvement in payment collection

### Quality Metrics
- 99% uptime
- Zero data loss incidents
- < 5% error rate

### Adoption Metrics
- Derek (primary user) uses system for 100% of sessions within 1 week
- 95% satisfaction rating
- 10+ additional coaches adopt within 3 months

## 9. Prioritization

### Must Have (P0) - MVP Launch
- Authentication (login/signup)
- Client CRUD operations
- Session scheduling and calendar
- Attendance tracking and notes
- Package/payment tracking
- Basic dashboard

### Should Have (P1) - V1.1
- Client search
- Client archive
- Recurring sessions
- Payment history
- Low balance alerts
- Revenue reports

### Could Have (P2) - V2
- Client portal
- Email notifications
- Payment processing
- Video notes
- Calendar sync

## 10. Open Questions

1. Should no-shows deduct from package balance? **Decision: Yes, configurable per session**
2. How to handle package expiration? **Decision: V2 feature**
3. Support for group sessions? **Decision: V2 feature**

## 11. Approval

- **Business Owner:** Derek Walsh - Approved
- **Development Team:** Pending Architecture Review
- **Next Step:** Create Technical Architecture Document

---

**Requirements Traceability:** 45 functional requirements defined  
**Status:** Ready for Architecture Phase
