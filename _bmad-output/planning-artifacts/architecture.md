# Technical Architecture: CoachConnect

**Version:** 1.0  
**Date:** 2026-02-22  
**Author:** Architect Agent  
**Product:** CoachConnect

## 1. Architecture Overview

CoachConnect is a modern, serverless web application built with Next.js and Supabase, optimized for rapid development and low operational overhead.

### Architecture Pattern
**Jamstack + Serverless Backend**
- Static-first frontend with dynamic data
- API routes for business logic
- Serverless database (PostgreSQL)
- Edge-deployed for performance

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
│  Next.js App (React) - Responsive Web Interface             │
│  • TailwindCSS for styling                                  │
│  • ShadCN UI components                                      │
│  • React Hook Form for forms                                │
└──────────────────┬──────────────────────────────────────────┘
                   │ HTTPS
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                    Application Layer                         │
│  Next.js App Router + API Routes                            │
│  • Server Components for data fetching                      │
│  • API routes for mutations                                 │
│  • Server-side rendering (SSR)                              │
└──────────────────┬──────────────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                      Data Layer                              │
│  Supabase (PostgreSQL + Auth + Storage)                     │
│  • Row Level Security (RLS)                                 │
│  • Real-time subscriptions                                  │
│  • Built-in authentication                                   │
└─────────────────────────────────────────────────────────────┘
```

## 2. Technology Stack

### Frontend
- **Framework:** Next.js 14+ (App Router, React Server Components)
- **Language:** TypeScript
- **Styling:** TailwindCSS + ShadCN UI
- **Forms:** React Hook Form + Zod validation
- **State:** React Context + Server State
- **Calendar:** React Big Calendar or similar

### Backend
- **Database:** PostgreSQL (via Supabase)
- **Auth:** Supabase Auth (email/password)
- **API:** Next.js API Routes
- **ORM:** Supabase Client (JavaScript SDK)

### Infrastructure
- **Hosting:** Vercel (frontend + API routes)
- **Database:** Supabase Cloud
- **Storage:** Supabase Storage (future: session videos)
- **Domain:** Custom domain via Vercel

### Development Tools
- **Version Control:** Git + GitHub
- **Package Manager:** npm
- **Linting:** ESLint + Prettier
- **Testing:** Vitest + React Testing Library (P1)

## 3. Data Model

### Entity Relationship Diagram

```
┌─────────────┐
│   coaches   │
├─────────────┤         ┌──────────────┐
│ id (PK)     │────┐    │   clients    │
│ email       │    │    ├──────────────┤
│ name        │    └───<│ id (PK)      │
│ created_at  │         │ coach_id (FK)│
└─────────────┘         │ name         │
                        │ age          │
                        │ email        │
                        │ phone        │
                        │ skill_level  │
                        │ goals        │
                        │ archived     │
                        │ created_at   │
                        └──────┬───────┘
                               │
                    ┌──────────┴───────────┬──────────────┐
                    │                      │              │
                    ▼                      ▼              ▼
            ┌──────────────┐      ┌──────────────┐  ┌─────────────┐
            │   sessions   │      │   packages   │  │   payments  │
            ├──────────────┤      ├──────────────┤  ├─────────────┤
            │ id (PK)      │      │ id (PK)      │  │ id (PK)     │
            │ client_id(FK)│      │ client_id(FK)│  │ client_id   │
            │ package_id   │      │ total_sessions│ │ package_id  │
            │ scheduled_at │      │ price        │  │ amount      │
            │ duration_min │      │ sessions_used│  │ paid_at     │
            │ status       │      │ created_at   │  │ method      │
            │ notes        │      │ active       │  │ created_at  │
            │ skills_worked│      └──────────────┘  └─────────────┘
            │ created_at   │
            └──────────────┘
```

### Schema Definitions

#### coaches
```sql
CREATE TABLE coaches (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### clients
```sql
CREATE TABLE clients (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  coach_id UUID REFERENCES coaches(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  age INTEGER,
  email TEXT,
  phone TEXT,
  skill_level TEXT CHECK (skill_level IN ('beginner', 'intermediate', 'advanced')),
  goals TEXT,
  archived BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_clients_coach_id ON clients(coach_id);
CREATE INDEX idx_clients_archived ON clients(coach_id, archived);
```

#### packages
```sql
CREATE TABLE packages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  client_id UUID REFERENCES clients(id) ON DELETE CASCADE,
  coach_id UUID REFERENCES coaches(id) ON DELETE CASCADE,
  total_sessions INTEGER NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  sessions_used INTEGER DEFAULT 0,
  active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  
  CONSTRAINT valid_sessions CHECK (sessions_used <= total_sessions)
);

CREATE INDEX idx_packages_client ON packages(client_id, active);
```

#### sessions
```sql
CREATE TABLE sessions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  client_id UUID REFERENCES clients(id) ON DELETE CASCADE,
  coach_id UUID REFERENCES coaches(id) ON DELETE CASCADE,
  package_id UUID REFERENCES packages(id) ON DELETE SET NULL,
  scheduled_at TIMESTAMPTZ NOT NULL,
  duration_minutes INTEGER DEFAULT 60,
  status TEXT CHECK (status IN ('scheduled', 'completed', 'cancelled', 'no-show')) DEFAULT 'scheduled',
  notes TEXT,
  skills_worked TEXT[],
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_sessions_coach_date ON sessions(coach_id, scheduled_at);
CREATE INDEX idx_sessions_client ON sessions(client_id, scheduled_at DESC);
CREATE INDEX idx_sessions_status ON sessions(coach_id, status, scheduled_at);
```

#### payments
```sql
CREATE TABLE payments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  client_id UUID REFERENCES clients(id) ON DELETE CASCADE,
  coach_id UUID REFERENCES coaches(id) ON DELETE CASCADE,
  package_id UUID REFERENCES packages(id) ON DELETE SET NULL,
  amount DECIMAL(10, 2) NOT NULL,
  paid_at TIMESTAMPTZ NOT NULL,
  payment_method TEXT,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_payments_coach ON payments(coach_id, paid_at DESC);
CREATE INDEX idx_payments_client ON payments(client_id, paid_at DESC);
```

## 4. Security Architecture

### Row Level Security (RLS)

All tables use Supabase RLS to ensure coaches only access their own data:

```sql
-- Clients: Coach can only see their own clients
CREATE POLICY "Coaches can view own clients"
  ON clients FOR SELECT
  USING (coach_id = auth.uid());

CREATE POLICY "Coaches can insert own clients"
  ON clients FOR INSERT
  WITH CHECK (coach_id = auth.uid());

CREATE POLICY "Coaches can update own clients"
  ON clients FOR UPDATE
  USING (coach_id = auth.uid());

-- Similar policies for sessions, packages, payments
```

### Authentication Flow

1. User signs up with email/password (Supabase Auth)
2. Supabase creates `auth.users` record
3. Trigger creates matching `coaches` record
4. Session JWT contains `coach_id` (from auth.uid())
5. All queries automatically filtered by coach_id via RLS

## 5. API Design

### API Routes (Next.js App Router)

```
/api/
├── auth/
│   ├── signup - POST - Create new coach account
│   └── signout - POST - End session
│
├── clients/
│   ├── GET - List all clients (with search/filter)
│   ├── POST - Create new client
│   ├── [id]/
│   │   ├── GET - Get client details
│   │   ├── PATCH - Update client
│   │   └── DELETE - Archive client
│   └── [id]/sessions - GET - Get client session history
│
├── sessions/
│   ├── GET - List sessions (filter by date range, status)
│   ├── POST - Create new session
│   ├── [id]/
│   │   ├── GET - Get session details
│   │   ├── PATCH - Update session (status, notes)
│   │   └── DELETE - Cancel session
│   └── dashboard - GET - Today's and upcoming sessions
│
├── packages/
│   ├── GET - List packages for client
│   ├── POST - Create new package
│   └── [id]/
│       ├── GET - Get package details
│       └── PATCH - Update package
│
└── payments/
    ├── GET - List payments (with filtering)
    ├── POST - Record new payment
    └── dashboard - GET - Payment summary and low balances
```

### Sample API Response

```typescript
// GET /api/clients/[id]
{
  "client": {
    "id": "uuid",
    "name": "Sarah Johnson",
    "age": 16,
    "email": "sarah@example.com",
    "phone": "555-0123",
    "skill_level": "intermediate",
    "goals": "Improve serving accuracy and blocking",
    "created_at": "2026-01-15T10:00:00Z"
  },
  "current_package": {
    "id": "uuid",
    "total_sessions": 10,
    "sessions_used": 7,
    "sessions_remaining": 3,
    "price": 500.00
  },
  "recent_sessions": [...]
}
```

## 6. Frontend Architecture

### Directory Structure

```
coachconnect/
├── app/
│   ├── (auth)/
│   │   ├── login/page.tsx
│   │   └── signup/page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx           # Dashboard shell with nav
│   │   ├── page.tsx              # Dashboard home
│   │   ├── clients/
│   │   │   ├── page.tsx          # Client list
│   │   │   ├── [id]/page.tsx     # Client detail
│   │   │   └── new/page.tsx      # New client form
│   │   ├── schedule/
│   │   │   └── page.tsx          # Calendar view
│   │   └── payments/
│   │       └── page.tsx          # Payment dashboard
│   └── api/                      # API routes (see above)
├── components/
│   ├── ui/                       # ShadCN components
│   ├── clients/
│   │   ├── ClientList.tsx
│   │   ├── ClientCard.tsx
│   │   └── ClientForm.tsx
│   ├── sessions/
│   │   ├── SessionCard.tsx
│   │   ├── SessionForm.tsx
│   │   └── SessionNotesModal.tsx
│   └── dashboard/
│       ├── DashboardStats.tsx
│       └── UpcomingSessions.tsx
├── lib/
│   ├── supabase.ts              # Supabase client
│   ├── auth.ts                  # Auth helpers
│   └── validations.ts           # Zod schemas
└── types/
    └── database.ts              # TypeScript types
```

### Key Design Patterns

**Server Components by Default**
- Fetch data in Server Components
- Client Components only when needed (forms, interactivity)

**Progressive Enhancement**
- Forms work without JavaScript
- Server Actions for mutations

**Optimistic UI**
- Client-side updates while server processes
- Revalidate on success/error

## 7. Deployment Architecture

### Environments

```
Development → preview.coachconnect.app (Vercel Preview)
     ↓
Staging → staging.coachconnect.app (Vercel)
     ↓
Production → app.coachconnect.com (Vercel)
```

### Deployment Pipeline

1. **PR Created** → Vercel deploys preview environment
2. **Merged to `dev`** → Deploys to staging
3. **Merged to `main`** → Deploys to production
4. **Database Migrations** → Run via Supabase CLI before deployment

### Environment Variables

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# App
NEXT_PUBLIC_APP_URL=
```

## 8. Performance Optimization

### Strategies
- **Static Generation:** Marketing pages
- **ISR (Incremental Static Regeneration):** Dashboard pages (revalidate: 60s)
- **Edge Caching:** API responses where appropriate
- **Database Indexes:** All foreign keys and common query paths
- **Image Optimization:** Next.js Image component
- **Code Splitting:** Automatic via Next.js

### Performance Targets
- **Lighthouse Score:** > 90
- **First Contentful Paint:** < 1.5s
- **Time to Interactive:** < 3s
- **API Response Time:** < 300ms (p95)

## 9. Testing Strategy

### Unit Tests (P1)
- Utility functions
- Validation schemas
- Business logic in lib/

### Integration Tests (P1)
- API routes
- Database queries
- Auth flows

### E2E Tests (P2)
- Critical user journeys (Playwright)
- Session creation flow
- Client management flow

## 10. Migration & Data Strategy

### Initial Setup
1. Coach signs up
2. Manually imports existing clients (CSV upload - P1)
3. Creates initial session packages
4. Starts using for all new sessions

### Backup Strategy
- Supabase automatic daily backups (7-day retention)
- Export to JSON weekly (P1)

## 11. Scalability Considerations

### Current Scale (MVP)
- 100 coaches
- 10,000 clients total (100 per coach)
- 50,000 sessions/month

### Bottlenecks & Solutions
- **Database:** PostgreSQL scales to 1M+ rows easily
- **Storage:** Supabase limits (if adding videos in V2)
- **Compute:** Serverless scales automatically

## 12. Future Technical Enhancements (V2)

- **Real-time updates:** Supabase subscriptions for live calendar
- **Mobile app:** React Native with shared API
- **Video storage:** Supabase Storage + video player
- **Email service:** Resend or SendGrid integration
- **Analytics:** PostHog or Mixpanel
- **Search:** Full-text search on client notes

## 13. Architecture Decision Records

### ADR-001: Why Next.js over separate frontend/backend?
**Decision:** Use Next.js App Router with API routes  
**Rationale:** Simpler deployment, better DX, built-in SSR/SEO  
**Alternatives:** Vite + Express, Remix

### ADR-002: Why Supabase over traditional backend?
**Decision:** Use Supabase for database + auth  
**Rationale:** Faster development, RLS for security, generous free tier  
**Alternatives:** PostgreSQL + custom auth, Firebase

### ADR-003: Why ShadCN over component library?
**Decision:** Use ShadCN UI components  
**Rationale:** Customizable, owns the code, Tailwind-native  
**Alternatives:** MUI, Chakra UI, Ant Design

## 14. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Supabase outage | High | Fallback to read-only mode, daily backups |
| Database schema changes | Medium | Use migrations, version control schema |
| Scaling beyond free tier | Low | Monitor usage, plan for paid tier ($25/mo) |

## 15. Success Metrics

### Technical KPIs
- 99.9% uptime
- < 2s page load time
- < 300ms API response (p95)
- Zero data loss

## 16. Next Steps

1. Set up Next.js project with TypeScript
2. Configure Supabase project and RLS policies
3. Implement database schema
4. Create UX designs and component library
5. Begin Epic 1 (Foundation & Auth)

---

**Status:** Architecture Approved  
**Next Phase:** UX Design
