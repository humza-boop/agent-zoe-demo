# Handoff: Agent Zoe — Client Portal (Clearfield Tax)

## Overview
Agent Zoe is an AI intake & coordination layer for tax/accounting firms. This package
covers the **client-facing portal**: the screens a tax client uses to complete an
organizer (intake questionnaire), upload documents, and track their return — with
**Zoe**, an AI concierge, available in-portal and over SMS.

Demo firm: **Clearfield Tax**. Demo clients: **Dana Chen** (returning, mid-flow) and
**Alex Rivera** (first-time, brand new). Tax year **2025**.

The product's promise is that the client always knows **where they are**, **what to do
next**, and **what happens next** — design decisions throughout serve those three questions.

## About the design files
The single file in this bundle — `index.html` — is a **design reference created in
HTML/CSS/vanilla JS**. It is a prototype demonstrating intended look, layout, copy, and
interaction — **not production code to ship**. The task is to **recreate these designs in
the target codebase's environment** (e.g. React/Next, Vue, etc.) using its established
component patterns, routing, state management, and styling system. If no front-end
environment exists yet, choose the most appropriate framework and implement there.

Everything in the prototype is one self-contained file with inline `<style>` and a single
`<script>`; in production this should become real components, routes, and a data layer.

## Fidelity
**High-fidelity.** Final colors, typography, spacing, copy, and interactions are intended
as shown. Recreate the UI faithfully using the codebase's libraries — match the tokens in
the **Design Tokens** section exactly. Where the prototype simulates back-end behavior
(see **Mocked vs. Real**), wire those to real services instead of copying the fakes.

---

## Screens / Views

All client screens share a shell: a **220px left sidebar** (logo + nav + client/sign-out)
and a flex **main** column with a **54px sticky top bar**. Below 860px the sidebar becomes
an **off-canvas drawer** opened by a hamburger in the top bar.

### 0. Login (`#screen-login`)
- **Purpose:** Sign in. Demo entry point.
- **Layout:** Full-viewport split — left **56%** cream form panel, right **44%** dark
  (`#141815`) marketing panel with two green radial glows. Stacks to one column < 860px
  (marketing panel hidden).
- **Components:** Clearfield logo; eyebrow "Welcome back"; H1 "Sign in to your account.";
  email + password inputs (white, 1px border, 6px radius, leading icon); dark "Continue"
  button; **demo persona chooser** — two buttons "Dana Chen / Returning · mid-flow" and
  "Alex Rivera / First-time · brand new"; "Create your account" link. Right panel:
  Fraunces 300 heading "Real guidance, all year long." + 3 bullets.
- **Behavior:** Choosing a persona stores it in `sessionStorage` and reloads into a
  pristine dashboard (see Mocked vs. Real → personas).

### 1. Dashboard / Home (`#screen-dashboard`)
- **Purpose:** Status-first home. Answers "where am I / what do I do / what's next."
- **Layout:** Sidebar + main. Main top-to-bottom:
  1. **Status hero card** — greeting + date; a short status sentence; a **derived % numeral**
     ("X% of the way to filing") + estimated time; a **horizontal 6-stage tracker**
     (Organizer → Documents → Under Review → In Progress → Sign & Review → Filed) with
     completed stages showing a **checkmark**, current stage filled, future stages dimmed.
     *No CTA in the hero — status only.*
  2. **Action required** — a single highest-priority next-step row (tag + title + sub +
     one CTA). Exactly one action.
  3. Two-column grid (`.dash-grid-row`, collapses < 900px): **Documents** card |
     **Recent activity** card. Both use the identical card + dot-row format.
- **Components:** status pills/dots use the semantic status colors; activity rows = dot
  rail + text + relative timestamp.

### 2. Organizer / Intake (`#screen-organizer`)
- **Purpose:** Structured questionnaire replacing PDF organizers.
- **Layout (desktop):** 3-column grid — sidebar (220) | **section-progress rail**
  (200px, "Organizer Progress" with the 5 sections + completion checks) | content (max
  640px). **Mobile:** rail is hidden; the section list moves **into the hamburger drawer**;
  a progress bar + "Section X of 5" eyebrow + Back/Continue carry navigation.
- **Sections:** Filing Profile, Life Changes, Income Sources, Deductions & Credits,
  Business Questions.
- **Core interaction — multi-select then expand:** Income/Deductions/Business ask one
  "select all that apply" question; checking an option **live-expands a deep-dive card**
  inline; unchecking closes it. Filing Profile & Life Changes are short direct forms.
- **Components:** "?" **Powered-by-Zoe** tooltips explain why each question matters;
  Yes/No toggles, text/number inputs, a states multiselect dropdown; top progress bar +
  "All changes saved" indicator; Back / Continue / "Save and exit".

### 3. Documents (`#screen-documents`)
- **Purpose:** Every required document, grouped by status, with upload.
- **Locked state:** Before the organizer is complete, shows "Finish your organizer first"
  + an "X of 5 sections complete" progress + a preliminary checklist.
- **Unlocked state:** A global drag/drop/upload zone, then documents grouped by status
  (**Needs attention** pinned first, then Needed, Under review, Approved). Each row: icon +
  title + sub + status pill (+ per-doc Upload button when needed). Flagged rows expand a
  **"Note from James"** with Re-upload / Reply to Zoe actions.
- **Upload:** Every upload control opens a real file picker
  (`accept="image/*,application/pdf"`) so mobile offers **Camera / Photo Library / Files**.

### 4. Zoe panel (overlay, all client screens)
- Floating "Ask Zoe" FAB → a chat panel with a static greeting + context quick-action
  chips. Single entry point; not a persistent rail.

### 5. Client Communications drawer (SMS mirror, overlay)
- A right-side drawer showing an **iMessage-style** thread from the **client's phone POV**:
  Zoe's messages incoming on the **left** (gray), the client's outgoing on the **right**
  (blue), header = "Zoe · Clearfield Tax". Portal is the source of truth; SMS mirrors
  status and carries links — never documents/PII.

### Auth utility screens
Signup (`#screen-signup`, split like login), Forgot password (`#screen-forgot-password`,
centered card), Prior-year upload (`#screen-upload-prior`, centered card). The first-time
user is offered prior-year upload when starting the organizer; uploading **autopopulates
Filing Profile** and advances to the next section.

### Not built yet (3 of the 6 journey stages)
**Return Status** (vertical-detailed tracker), **Sign & Review** (refund reveal — the
emotional peak, intended in Fraunces), and **Filed** (confirmation). They appear as
dimmed/upcoming stages in the hero tracker but have no screens.

---

## Interactions & Behavior
- **Screen navigation:** `showScreen(id)` with a brief fade overlay; sidebar nav links,
  hero/action CTAs, and deep-links (`goToDocument(id)`) all route through it.
- **Organizer multi-select:** checking an option toggles a `.checked` class and live-reveals
  its deep-dive card; progress bar + sidebar status recompute immediately.
- **Document upload (simulated):** upload → status `uploaded` ("Under review") → after
  ~1.5s the first doc is **flagged** (demonstrates the flag flow), subsequent ones
  **approved**. Each transition logs to Recent Activity and may mirror an SMS from Zoe.
- **Zoe SMS:** canned keyword replies; sending a text logs "You texted Zoe: …" to Recent
  Activity and Zoe's reply appears moments later. All activity copy is in the client's voice.
- **Responsive:** ≥1180 full; 720–1180 single column with rail; <860 sidebar → off-canvas
  drawer + hamburger; login stacks; Documents/upload are touch- and camera-friendly.
- **Motion:** default `0.25s ease`; reveals fade + translateY via
  `cubic-bezier(0.22,0.61,0.36,1)`; respect `prefers-reduced-motion` in production.

## State Management
Prototype state (replace with real models/stores):
- `currentClientKey` ('dana' | 'new') + `CLIENTS` identity map.
- `orgSectionStatus` — per-section: `notstarted | active | complete`; multi-select choices
  read from `.org-ms-option.checked` in the DOM.
- `docState` — per required document: `needed | uploaded | approved | flagged` (+ note,
  event history). Required docs are **derived** from organizer answers (`requiredDocs()`).
- `activityLog` + `zoeActivityLog` — merged, newest-first, into the unified Recent Activity feed.
- `smsThread` — message list for the comms drawer.
- **Journey stage & %** are **computed** (`computeJourneyStage()`, stage-weighted) — never
  stored as a parallel signal. Map this to the canonical **S0–S9 state machine + data
  model** in the product docs.

## Design Tokens
All defined in `:root` in `portal.html`. Exact values:

**Color**
| Token | Value | Use |
|---|---|---|
| `--paper` | `#f4f0e8` | page background |
| `--fg` | `#232220` | primary text |
| `--muted` | `#6e6a62` | secondary text |
| `--faint` | `#a39d92` | tertiary / labels |
| `--hair` | `rgba(35,34,32,0.12)` | borders / dividers |
| `--panel-strong` | `#ffffff` | card surface |
| `--btn-bg` / `--btn-fg` | `#232220` / `#f6f3ec` | primary button |
| `--accent` / `--accent2` | `#51624f` / `#8a9a86` | forest accent / sage |
| `--accent-tint` | `rgba(81,98,79,0.08)` | accent wash (active nav, icons) |
| `--warn` / `--warn-tint` | `#c0621a` / `rgba(192,98,26,0.08)` | warm/attention |
| `--ok` / `--ok-tint` | `#3a6b43` / `rgba(58,107,67,0.08)` | success |
| `--danger` / `--danger-tint` | `#9a3b2e` / `rgba(154,59,46,0.10)` | destructive (defined, unused) |
| Dark theme | `--dark-paper #141815`, `--dark-fg #edeae2`, `--dark-muted #a7aca2`, `--dark-faint #6f746c` | login marketing panel / high-impact |

**Semantic status** (the one pill + dot system): `--status-needed` → warm,
`--status-review` → neutral muted, `--status-approved` → green, `--status-flagged` → warm,
each with a matching `*-tint`. Labels (sentence case): **Needed · Under review · Approved ·
Needs attention**. *Overdue & error states are not yet defined — add when introduced.*

**Spacing** (8px base): `--space-1..8` = 4, 8, 12, 16, 24, 32, 40, 48px.

**Radius:** `--radius-pill` 999px · `--radius-card` 10px · `--radius-control` 7px ·
`--radius-button` 4px.

**Type scale** (Inter): page-title 28 · section 22 · card-title 19 · body 16 · small 14 ·
label 12.5px. **Fraunces 300** (`--text-emotional`, ~34px) is reserved for the login
marketing heading and the (unbuilt) refund/filed moments — **not** routine UI. Min body 14,
min interactive text 14.

**Shadow:** `--shadow-card` `0 1px 2px rgba(35,34,32,.04), 0 4px 16px rgba(35,34,32,.06)`;
`--shadow-card-hover` slightly stronger (interactive cards only).

**Motion:** `--transition` `0.25s ease`; reveal ease `cubic-bezier(0.22,0.61,0.36,1)`.

**Layout:** `--sidebar-w` 220px; `--zoe-w` 296px. Breakpoints (in media queries, not vars):
**900px** (dashboard grid → 1 col), **860px** (sidebar → off-canvas, login stacks),
**600px** (top-bar condenses).

## ⚠️ Mocked vs. Real — replace before shipping
These are demo fakes; do **not** carry them into production:
- **Personas / `sessionStorage` + reload** — demo account switching, not real auth/accounts.
- **`getZoeSmsReply()`** — canned keyword matching, not a real AI/agent. Wire to the actual
  Zoe service.
- **Simulated timers** — upload "uploading…", review flag/approve, typing indicators,
  "signing in…" delays. Replace with real async state.
- **Autopopulate** — prior-year upload fills hardcoded demo data; real version parses an
  uploaded return.
- **"All changes saved", notifications, login** — all simulated; need real persistence,
  notifications, and authentication.
- **Required-document derivation** is real logic worth keeping conceptually, but should map
  to the firm's actual document rules + the **S0–S9 state machine / data model** in the docs.

## Accessibility (open items for production)
- Add focus rings, keyboard nav, and `aria-label`s on icon-only buttons (sign-out, notif,
  Zoe send, hamburger).
- Verify contrast and ≥44px hit targets, especially on mobile.

## Assets
- **Fonts:** Inter (400/500/600) and Fraunces (300/400/500) via Google Fonts — see the
  `<link>` in `portal.html`. Production should self-host or use the codebase's font setup.
- **Icons:** all inline SVG (no icon library, no raster images).
- **Images:** none — the dark login panel uses CSS radial gradients only.

## Files
- `index.html` — the complete single-file prototype: all screens, styles (one `:root`
  token block + component CSS), and behavior. Search by `#screen-*` ids and the
  `/* ─── … ─── */` section comments to navigate.
