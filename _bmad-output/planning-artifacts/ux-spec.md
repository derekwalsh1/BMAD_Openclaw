# UX Design Specification: CoachConnect

**Version:** 1.0  
**Date:** 2026-02-22  
**Author:** UX Designer Agent  
**Product:** CoachConnect

## 1. Design Principles

### Core Principles
1. **Mobile-First:** Coach needs to use on phone at the court
2. **Efficiency:** Every action ≤ 3 clicks
3. **Clarity:** Clear visual hierarchy, readable at a glance
4. **Speed:** Fast interactions, minimal loading states
5. **Confidence:** Clear feedback for all actions

## 2. Design System

### Color Palette

```
Primary (Brand):
- Primary 600: #2563eb (Blue - trust, professional)
- Primary 700: #1d4ed8 (Hover states)
- Primary 50: #eff6ff (Backgrounds)

Semantic Colors:
- Success: #16a34a (Green - completed sessions)
- Warning: #ea580c (Orange - low balance alerts)
- Error: #dc2626 (Red - cancelled, no-shows)
- Info: #0891b2 (Cyan - informational messages)

Neutrals:
- Gray 950: #030712 (Primary text)
- Gray 600: #4b5563 (Secondary text)
- Gray 300: #d1d5db (Borders)
- Gray 50: #f9fafb (Backgrounds)
- White: #ffffff

Status Colors:
- Scheduled: #3b82f6 (Blue)
- Completed: #16a34a (Green)
- Cancelled: #6b7280 (Gray)
- No-show: #dc2626 (Red)
```

### Typography

```
Font Family: Inter (system fallback: -apple-system, sans-serif)

Headings:
- H1: 2rem (32px), font-weight: 700
- H2: 1.5rem (24px), font-weight: 600
- H3: 1.25rem (20px), font-weight: 600

Body:
- Large: 1rem (16px), font-weight: 400
- Regular: 0.875rem (14px), font-weight: 400
- Small: 0.75rem (12px), font-weight: 400

Line Height: 1.5 (body), 1.2 (headings)
```

### Spacing Scale

```
Base unit: 4px (0.25rem)

Scale:
- xs: 4px
- sm: 8px
- md: 16px
- lg: 24px
- xl: 32px
- 2xl: 48px
```

### Component Specifications

#### Button
```
Primary Button:
- Background: primary-600
- Text: white, font-weight: 500
- Padding: 12px 24px
- Border-radius: 8px
- Hover: primary-700
- Active: primary-800

Secondary Button:
- Background: white
- Border: 1px solid gray-300
- Text: gray-700
- Hover: gray-50

Icon Button:
- Size: 40x40px
- Border-radius: 8px
- Icon: 20x20px
```

#### Card
```
- Background: white
- Border: 1px solid gray-200
- Border-radius: 12px
- Padding: 16px
- Shadow: 0 1px 3px rgba(0,0,0,0.1)
- Hover: shadow-md
```

#### Form Input
```
- Height: 44px
- Border: 1px solid gray-300
- Border-radius: 8px
- Padding: 12px 16px
- Focus: border-primary-600, ring-2 primary-100
- Error: border-error, ring-2 error-100
```

## 3. Navigation Structure

```
┌────────────────────────────────────────────────┐
│  CoachConnect Logo         👤 Derek ▼          │ ← Top Nav
├────────────────────────────────────────────────┤
│  📊 Dashboard │ 👥 Clients │ 📅 Schedule │ 💰 $ │ ← Main Nav Tabs
├────────────────────────────────────────────────┤
│                                                │
│              [Page Content]                    │
│                                                │
└────────────────────────────────────────────────┘
```

### Navigation Tabs (Desktop & Mobile)

| Icon | Label | Route | Description |
|------|-------|-------|-------------|
| 📊 | Dashboard | `/dashboard` | Today's schedule, quick stats |
| 👥 | Clients | `/clients` | Client list and management |
| 📅 | Schedule | `/schedule` | Calendar view |
| 💰 | Payments | `/payments` | Payment tracking |

## 4. Screen Specifications

### 4.1 Dashboard (Home)

**Purpose:** At-a-glance view of today's activities and key metrics

**Layout:**
```
┌──────────────────────────────────────────────────┐
│  🌅 Good morning, Derek!                         │
│  Friday, February 22, 2026                       │
├──────────────────────────────────────────────────┤
│  📊 Quick Stats (Row of 4 cards)                │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐           │
│  │  15  │ │  32  │ │  3   │ │ $850 │           │
│  │Today │ │Active│ │ Low  │ │ This │           │
│  │      │ │Clients│ │Balance│ │ Week│           │
│  └──────┘ └──────┘ └──────┘ └──────┘           │
├──────────────────────────────────────────────────┤
│  📅 Today's Sessions (4)        [+ New Session] │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 🕐 2:00 PM - Sarah Johnson               │   │
│  │ 📍 Skills: Serving, Passing               │   │
│  │ 📝 Last note: Great progress on float...  │   │
│  │ [View] [Start Session →]                  │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 🕐 3:30 PM - Mike Chen                    │   │
│  │ ⚠️ Package: 2 sessions remaining          │   │
│  │ [View] [Start Session →]                  │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
├──────────────────────────────────────────────────┤
│  📍 Upcoming This Week (6 sessions)             │
│  • Sat 2/23: 3 sessions                         │
│  • Sun 2/24: 2 sessions                         │
│  • Mon 2/25: 1 session                          │
└──────────────────────────────────────────────────┘
```

**Components:**
- Greeting header with date
- 4 metric cards (responsive grid)
- Today's sessions list with session cards
- Upcoming sessions summary
- "New Session" button (floating action button on mobile)

**Interactions:**
- Click session → Opens session detail modal
- "Start Session" → Quick mark complete + add notes
- Click client name → Navigate to client detail
- Click metric card → Navigate to relevant section

### 4.2 Client List

**Purpose:** View, search, and manage all clients

**Layout:**
```
┌──────────────────────────────────────────────────┐
│  👥 Clients (32)                  [+ New Client] │
├──────────────────────────────────────────────────┤
│  🔍 Search clients...                 ⚙️ Filter  │
├──────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────┐   │
│  │ Sarah Johnson          🟢 Active          │   │
│  │ Age 16 • Intermediate                     │   │
│  │ Package: 3/10 sessions • $150 balance    │   │
│  │ Next: Today 2:00 PM                       │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ Mike Chen              ⚠️ Low Balance     │   │
│  │ Age 14 • Advanced                         │   │
│  │ Package: 2/10 sessions • Paid             │   │
│  │ Next: Sat 10:00 AM                        │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  [Load More...]                                 │
└──────────────────────────────────────────────────┘
```

**Components:**
- Header with count and "New Client" button
- Search input (real-time filter)
- Filter options (skill level, active/archived, balance status)
- Client cards (scrollable list)
- Empty state if no clients

**Client Card Data:**
- Name (bold)
- Status badge (Active, Low Balance, Archived)
- Age + Skill level
- Current package status
- Next session (if scheduled)

**Interactions:**
- Click card → Navigate to client detail page
- Search → Filter list in real-time
- Filter → Show/hide based on criteria

### 4.3 Client Detail

**Purpose:** View all client information, sessions, and manage packages

**Layout:**
```
┌──────────────────────────────────────────────────┐
│  ← Back to Clients                               │
├──────────────────────────────────────────────────┤
│  Sarah Johnson                           [Edit]  │
│  Age 16 • Intermediate • 📧 📞                   │
│  Goal: Improve serving accuracy and blocking    │
├──────────────────────────────────────────────────┤
│  [Overview] [Sessions] [Packages] [Notes]        │ ← Tabs
├──────────────────────────────────────────────────┤
│  📦 Current Package                              │
│  ┌─────────────────────────────────────────┐   │
│  │  10 Sessions for $500                     │   │
│  │  Progress: ███████░░░ 7/10 (70%)          │   │
│  │  Sessions Remaining: 3                    │   │
│  │  ⚠️ Low - Consider renewal                │   │
│  │  [Add Payment] [New Package]               │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  📅 Upcoming Sessions (2)     [+ Schedule]      │
│  • Today 2:00 PM                                │
│  • Sat 2/23 10:00 AM                            │
│                                                  │
│  📊 Recent Activity                             │
│  ┌─────────────────────────────────────────┐   │
│  │ ✅ Feb 20 - Session Completed             │   │
│  │ Worked on: Serving, Passing                │   │
│  │ Great progress on float serve accuracy!    │   │
│  │ Next focus: Jump serve technique           │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  📈 Progress Summary                            │
│  • Total sessions: 24 (lifetime)                │
│  • Attendance rate: 95%                         │
│  • Total paid: $1,200                           │
└──────────────────────────────────────────────────┘
```

**Tabs:**
1. **Overview** - Current package, upcoming, recent activity
2. **Sessions** - Full session history with notes
3. **Packages** - All packages (active + past)
4. **Notes** - Aggregated notes and progress tracking (P1)

**Interactions:**
- Edit → Open client edit form
- Add Payment → Payment recording modal
- New Package → Create package form
- Schedule → Create session form
- Click session → Session detail

### 4.4 Schedule (Calendar View)

**Purpose:** Visual calendar of all sessions

**Layout:**
```
┌──────────────────────────────────────────────────┐
│  📅 Schedule                  [+ New Session]    │
├──────────────────────────────────────────────────┤
│  ← Feb 2026 →          [Week] [Month] [Day]     │
├──────────────────────────────────────────────────┤
│                                                  │
│  Week View (Default):                           │
│  ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐  │
│  │ Mon │ Tue │ Wed │ Thu │ Fri │ Sat │ Sun │  │
│  ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤  │
│  │     │     │     │     │ 2PM │ 10AM│     │  │
│  │     │     │     │     │Sarah│Mike │     │  │
│  │     │     │     │     │─────│─────│     │  │
│  │     │     │     │     │3:30 │ 2PM │     │  │
│  │     │     │     │     │Mike │Lisa │     │  │
│  └─────┴─────┴─────┴─────┴─────┴─────┴─────┘  │
│                                                  │
│  Color coding:                                  │
│  🟦 Scheduled  🟩 Completed  🟥 No-show  ⬜ Cancelled │
└──────────────────────────────────────────────────┘
```

**Views:**
- **Week View** (default) - 7-day grid with time slots
- **Month View** - 30-day overview with session counts
- **Day View** - Single day with hourly breakdown

**Interactions:**
- Click time slot → Create new session
- Click session → Edit/view details
- Drag session → Reschedule (P1)
- Click client name → Go to client detail

### 4.5 Session Detail Modal

**Purpose:** View and complete sessions with notes

**Layout:**
```
┌─────────────────────────────────────────────┐
│  Session: Sarah Johnson           [✕ Close]│
├─────────────────────────────────────────────┤
│  📅 Friday, Feb 22, 2026                   │
│  🕐 2:00 PM - 3:00 PM (60 min)             │
│  📦 Package: 7/10 sessions used            │
├─────────────────────────────────────────────┤
│  Status: [Scheduled ▼]                     │
│  • Scheduled                               │
│  • ✅ Completed                            │
│  • ❌ No-show                              │
│  • 🚫 Cancelled                            │
├─────────────────────────────────────────────┤
│  ✍️ Session Notes                          │
│  ┌───────────────────────────────────────┐ │
│  │ Skills Worked (select tags):          │ │
│  │ [Serving] [Passing] [x Hitting]       │ │
│  │ [x Setting] [Blocking] [Defense]      │ │
│  └───────────────────────────────────────┘ │
│                                            │
│  ┌───────────────────────────────────────┐ │
│  │ Feedback & Progress:                  │ │
│  │ [Text area - large]                   │ │
│  │                                       │ │
│  └───────────────────────────────────────┘ │
│                                            │
│  ┌───────────────────────────────────────┐ │
│  │ Next Session Focus:                   │ │
│  │ [Text area - medium]                  │ │
│  └───────────────────────────────────────┘ │
│                                            │
│  📝 Previous Notes (read-only preview)     │
│  Feb 18: Worked on jump serve approach... │
│                                            │
├─────────────────────────────────────────────┤
│     [Cancel]      [Save & Mark Complete]   │
└─────────────────────────────────────────────┘
```

**Quick Actions:**
- Mark as completed → Auto-saves, deducts from package, returns to dashboard
- Add notes → Saves with session
- Previous notes → Shows context for continuity

### 4.6 New Client Form

**Purpose:** Add new client to roster

**Layout:**
```
┌─────────────────────────────────────────────┐
│  Add New Client                   [✕ Close]│
├─────────────────────────────────────────────┤
│  Basic Information                         │
│  ┌───────────────────────────────────────┐ │
│  │ Full Name *                           │ │
│  └───────────────────────────────────────┘ │
│                                            │
│  ┌─────────┐  ┌──────────────────────────┐│
│  │ Age     │  │ Skill Level *            ││
│  └─────────┘  │ ○ Beginner               ││
│               │ ○ Intermediate           ││
│               │ ○ Advanced               ││
│               └──────────────────────────┘ │
│                                            │
│  Contact Information                       │
│  ┌───────────────────────────────────────┐ │
│  │ Email                                 │ │
│  └───────────────────────────────────────┘ │
│  ┌───────────────────────────────────────┐ │
│  │ Phone                                 │ │
│  └───────────────────────────────────────┘ │
│                                            │
│  Goals & Notes                            │
│  ┌───────────────────────────────────────┐ │
│  │ What are the athlete's goals?         │ │
│  │ [Text area]                           │ │
│  └───────────────────────────────────────┘ │
│                                            │
│  Initial Package (optional)               │
│  ┌─────────┐  ┌─────────┐                │
│  │ Sessions│  │ Price $ │                │
│  └─────────┘  └─────────┘                │
│                                            │
├─────────────────────────────────────────────┤
│     [Cancel]            [Create Client]    │
└─────────────────────────────────────────────┘
```

**Validation:**
- Name required
- Skill level required
- Email format validated
- Phone format validated (optional)

**Flow:**
1. Fill form → Create Client
2. Success → Navigate to client detail page
3. Option to schedule first session

### 4.7 Payment Dashboard

**Purpose:** Track payments and outstanding balances

**Layout:**
```
┌──────────────────────────────────────────────────┐
│  💰 Payments                 [+ Record Payment]  │
├──────────────────────────────────────────────────┤
│  📊 Summary                                      │
│  ┌──────┐ ┌──────┐ ┌──────┐                    │
│  │ $850 │ │ $450 │ │  3   │                    │
│  │ This │ │ This │ │Clients│                    │
│  │ Week │ │ Month│ │ Owe  │                    │
│  └──────┘ └──────┘ └──────┘                    │
├──────────────────────────────────────────────────┤
│  ⚠️ Outstanding Balances (3)                    │
│  ┌─────────────────────────────────────────┐   │
│  │ Mike Chen                                 │   │
│  │ Package: $250 • Paid: $100 • Owe: $150   │   │
│  │ [Record Payment]                          │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  📝 Recent Payments                             │
│  • Feb 22 - Sarah Johnson - $500 (Venmo)        │
│  • Feb 20 - Lisa Park - $200 (Cash)             │
│  • Feb 18 - Mike Chen - $100 (Zelle)            │
│                                                  │
│  [View All Payments →]                          │
└──────────────────────────────────────────────────┘
```

**Features:**
- Payment summary cards
- Outstanding balances list
- Recent payment history
- Quick payment recording

## 5. Mobile Considerations

### Mobile Navigation
- Bottom navigation bar (tabs)
- Hamburger menu for secondary actions
- Pull-to-refresh on lists

### Mobile Optimizations
- Tap targets ≥ 44x44px
- Sticky headers on scroll
- Swipe gestures (swipe session card to complete)
- Native date/time pickers
- Auto-zoom disabled on inputs

### Responsive Breakpoints
```
Mobile: < 640px (single column)
Tablet: 640px - 1024px (2 columns)
Desktop: > 1024px (3 columns, sidebar)
```

## 6. Loading & Empty States

### Loading States
- Skeleton screens (pulse animation)
- Inline spinners for actions
- Optimistic UI updates

### Empty States
```
No Clients Yet
━━━━━━━━━━━━━
    👥
  
Add your first client to
get started tracking sessions

[+ Add Client]
```

## 7. Accessibility (WCAG 2.1 AA)

- Keyboard navigation (Tab, Enter, Esc)
- Focus indicators (2px outline)
- ARIA labels on icons
- Color contrast ≥ 4.5:1
- Screen reader tested
- Alt text on images

## 8. Interactions & Micro-animations

### Button States
- Hover: Slight darkening
- Active: Scale down 2%
- Loading: Spinner replaces text

### Transitions
- Page transitions: Fade 150ms
- Modal: Slide up from bottom (mobile), fade (desktop)
- Toast notifications: Slide in from top

### Feedback
- ✅ Success toast (green, 3s auto-dismiss)
- ❌ Error toast (red, manual dismiss)
- ℹ️ Info toast (blue, 5s auto-dismiss)

## 9. Design Assets Needed

### Icons
- Use Lucide Icons (consistent, open-source)
- All icons 20x20px default
- Outlined style for consistency

### Illustrations
- Empty states (clients, sessions, payments)
- Error states (404, 500)
- Onboarding tutorial (P1)

## 10. Design Checklist

Before handoff to development:
- [ ] All screens designed (Desktop + Mobile)
- [ ] Component library defined
- [ ] Interactive prototype (Figma)
- [ ] Accessibility audit
- [ ] Design tokens exported
- [ ] Dark mode specifications (P2)

## 11. Future UX Enhancements (V2)

- Dark mode toggle
- Customizable dashboard widgets
- Advanced filtering/sorting
- Bulk actions (multi-select sessions)
- Offline mode (PWA)
- Voice notes for sessions
- Photo/video upload with previews

---

**Status:** UX Specification Complete  
**Next:** Create Epics & Stories for Implementation
