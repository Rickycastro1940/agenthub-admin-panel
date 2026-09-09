# AgentHub Admin Panel — Product Specification

This file is the source of truth for the prototype. An implementer should be able to build the panel from this document alone.

**Process**

1. Commit this specification before any AgentHub `index.html` work.
2. After this draft exists, paste `STITCH-PROMPT.md` into [Google Stitch](https://stitch.withgoogle.com) to generate an initial visual proposal.
3. Treat Stitch output as look-and-feel reference only. Manually implement `index.html` so every requirement in this file is satisfied. Do not ship Stitch HTML.

**Deliverable:** one `index.html` at the repository root, based on the [html-hello](https://github.com/4GeeksAcademy/html-hello) template. Serve with `python3 server.py` (Flask static server on port 3000) or any static file server.

---

## 1. Product description

AgentHub is a SaaS marketplace where companies rent pre-configured AI agents and equip them with skills such as web research, document analysis, and calendar management.

This prototype is the **internal admin panel** used by AgentHub operations staff to:

- monitor platform health and rental revenue
- manage customer accounts
- manage rented agents and their system prompts
- maintain the skill catalog
- inspect rental contracts and itemized pricing
- investigate execution errors

The prototype is a reviewable frontend reference only. It uses realistic hardcoded data and does not connect to a backend.

**Signed-in operator (display only, no login screen):** Jordan Hale, Operations. Environment: Production.

---

## 2. Technology and constraints

| Allowed | Not allowed |
|---------|-------------|
| A single `index.html` file | Extra app HTML pages |
| Semantic HTML (`header`, `nav`, `main`, `section`, `table`, `dialog` or equivalent) | React, Vue, Svelte, Alpine.js, jQuery |
| Tailwind CSS via official CDN script | Custom CSS files, `<style>` blocks, inline `style` attributes |
| Vanilla JavaScript inside `index.html` | Package managers, bundlers, UI component libraries |
| Hardcoded / in-memory mock data | REST APIs, databases, real auth, billing processors |
| Google Fonts for Inter (optional) | Charts.js, Chart.css, or any chart library that needs inline `--size` styles |

**Viewport:** Desktop and tablet first. Wide tables sit in a horizontal overflow wrapper. Mobile stacking is welcome but not the primary target.

**Accessibility:** Skip link to main content, visible focus rings, `button` elements for interactive controls, labeled form fields, `aria-expanded` / `aria-haspopup` on menus, dialog attributes on modals, and captions or `sr-only` text for the weekly activity visualization.

---

## 3. Information architecture and shared shell

### 3.1 Sidebar

Persistent left sidebar with:

- AgentHub mark (`AH`) and product name
- Navigation links, in this order: **Dashboard**, **User Management**, **Agent Management**, **Skills**, **Agent Contracts**, **Error Log**

Selecting a nav item:

1. Reveals the matching section in the main content area (no page reload)
2. Updates the URL hash (`#dashboard`, `#users`, `#agents`, `#skills`, `#contracts`, `#errors`)
3. Applies a clearly visible active state to **exactly one** navigation item

Deep links must restore the matching section on load. Unknown or empty hash defaults to Dashboard.

### 3.2 Top bar

Persistent top bar with:

- Current section title
- Compact environment indicator: `Production`
- Light / dark theme toggle (icon button with an accessible name)

### 3.3 Theme

- Theme applies to the complete shell and every section using Tailwind `dark:` utilities.
- Theme remains intact while switching sections.
- Persist the choice in `localStorage` key `agenthub-theme` with values `light` or `dark`.
- Default is `light` unless stored value is `dark`.

### 3.4 Visual language

Calm professional operations dashboard:

- Navy sidebar (`slate-900` / `blue-950` family)
- Slate page surfaces
- Blue primary actions
- Rounded cards, restrained shadows, 1px borders
- Color-coded health states (emerald success, amber caution, rose danger, slate neutral)

Stitch may propose a different palette. If it conflicts with this section, **this section wins**.

---

## 4. Section specifications

### 4.1 Dashboard (`#dashboard`)

1. Four metric cards: **Monthly revenue**, **Discount losses**, **Active agents**, **Failing agents**. Every card includes an icon, label, hardcoded value, contextual comparison text, and a distinct accent color.
2. Layout: four-column grid on desktop, two-column grid on tablet. Cards use a subtle border and shadow and remain legible in both themes.
3. Full-width **Weekly Activity** panel under the cards: a hardcoded seven-day bar visualization (Mon–Sun), a legend, and an accessible text summary. Bars must be sized with Tailwind height utilities, not inline styles.
4. Below the chart: **Recent Activity** list beside a **Platform Health** summary so the first view communicates both business activity and operational status.

**Dashboard seed values**

| Metric | Value | Comparison | Accent |
|--------|-------|------------|--------|
| Monthly revenue | $48,290 | +12.4% vs last month | Blue |
| Discount losses | $3,180 | +6.2% coupon leakage | Amber |
| Active agents | 3 | 4 rented, 1 paused | Emerald |
| Failing agents | 1 | Atlas Research needs review | Rose |

Weekly activity (relative bar heights): Mon 40%, Tue 55%, Wed 62%, Thu 78%, Fri 90%, Sat 48%, Sun 35%. Legend: Agent runs (navy/blue) vs Errors (rose).

### 4.2 User Management (`#users`)

1. Section summary: total users, active users, trial users.
2. Responsive table with **at least five** users and columns: name/avatar, email, plan, status, join date, actions.
3. Plan and account state render as compact semantic badges.
4. Every row has a `⋮` action button whose menu contains **View detail** and **Delete**. Only one action menu can be open. Selecting the trigger again or clicking outside closes it.
5. **View detail** opens a modal with the complete user record: contact details, billing plan, company, account state, join date, and currently rented agents.
6. **Delete** removes the row from the in-memory list, shows a toast, and is not persisted across refresh.

### 4.3 Agent Management (`#agents`)

1. At least **four** agent cards: name, avatar initials, owner company, status badge, last-run information, and a collapsed associated-skill list.
2. Skill lists are hidden by default. A labeled expand control reveals skills by transitioning maximum height, opacity, and chevron orientation; selecting it again collapses the content.
3. Shared identities (required): **Nora Finance**, **Atlas Research**, **Calendar Concierge**, and **DealDesk Copilot** also appear where relevant in contracts and errors.
4. Each agent has a `⋮` menu with **Configure** and **Delete**.
5. **Configure** opens a modal containing the agent’s editable system prompt in a labeled `textarea`, plus **Cancel** and **Save changes**. Save updates in-memory data and shows a toast.

### 4.4 Skills (`#skills`)

1. An explanatory callout: a skill is a reusable capability that can be enabled on one or more rented AI agents.
2. At least **four** skill cards: icon, name, short description, category badge, enabled-agent count, and a hardcoded usage indicator (for example a small bar or percentage).
3. Each skill has a `⋮` menu with **View detail** and **Delete**.
4. **View detail** opens a modal with description, category, permission scope, enabled agents, version, and last updated date.

### 4.5 Agent Contracts (`#contracts`)

1. Table with at least **four** active or completed rental contracts. Columns: contract identifier/client, agent, contracted skills, term dates, status, amount paid, actions.
2. Skills appear as compact chips. Dates use a consistent human-readable format (for example `Mar 1, 2026`). Currency values align for comparison.
3. Each row has a `⋮` menu containing **View detail**.
4. The modal shows client, agent, term, status, base rental fee, itemized skill names and individual prices, discount if applicable, and total paid.
5. Contract totals and skill names must agree with the associated itemized modal breakdown.

### 4.6 Error Log (`#errors`)

1. At least **six** hardcoded error entries: timestamp, agent name, severity/type badge, short description, resolution state, actions.
2. Distinct badges for **Critical**, **Integration**, **Timeout**, **Permission**, and **Validation**. Resolved entries are visually muted without losing readability.
3. Each row has a `⋮` menu with **View detail** and **Mark as resolved**. Resolving updates the row badge and disables the resolve action for the current browser session (in-memory only).
4. **View detail** opens a modal with error identifier, agent, timestamp, severity, request/context summary, and a readable full trace block.

---

## 5. Component inventory

| Component | Contents | Behavior |
|-----------|----------|----------|
| Application shell | Sidebar, top bar, main viewport | Persistent across all six sections |
| Sidebar nav item | Icon, label, active indicator | Hash routing; one active item |
| Metric card | Icon tile, label, value, comparison | Distinct accent; dashboard grid |
| Status badge | Pill text | Semantic colors reused everywhere |
| Action dropdown | `⋮` trigger + menu | One open at a time; outside click closes |
| Modal dialog | Backdrop, title, body, close, optional footer | Close via close button, backdrop, Escape |
| Collapsible skill list | Expand button, animated region, chips | Starts collapsed |
| Data table | Overflow wrapper, header, rows, actions | Tablet/desktop; horizontal scroll if needed |
| Theme toggle | Labeled icon button | Applies `dark` class and stores preference |
| Toast | Transient message | Save, delete, and resolve feedback |
| Weekly bars | Seven labeled bars + legend | Tailwind heights only |

Implement each interactive pattern once and reuse it.

---

## 6. Exact seed data

Use these records on first load. Refresh restores them except theme preference.

### 6.1 Users

| id | name | email | company | plan | status | joined | rented agents |
|----|------|-------|---------|------|--------|--------|----------------|
| u1 | Jordan Hale | jordan@acmelabs.io | Acme Labs | Growth | Active | Nov 2, 2025 | Nora Finance |
| u2 | Priya Shah | priya@northstar.example | Northstar Inc. | Scale | Active | Aug 18, 2025 | Atlas Research |
| u3 | Luis Romero | luis@harborfreight.example | Harbor Freight Co. | Starter | Trial | Jul 12, 2026 | Calendar Concierge |
| u4 | Elena Rossi | elena@dealdesk.example | DealDesk LLC | Growth | Active | Jan 9, 2026 | DealDesk Copilot |
| u5 | Maya Chen | maya@lumenhealth.example | Lumen Health | Starter | Suspended | Mar 22, 2026 | — |

Summary derived from this table: **5** total, **3** active, **1** trial.

### 6.2 Agents

| id | name | owner | status | last run | skills | system prompt (initial) |
|----|------|-------|--------|----------|--------|-------------------------|
| a1 | Nora Finance | Acme Labs | Active | 2 hours ago | Document Analysis, Web Research, Invoice Matching | You are Nora Finance. Reconcile invoices, flag anomalies, and never invent totals. |
| a2 | Atlas Research | Northstar Inc. | Failing | 18 minutes ago | Web Research, Document Analysis | You are Atlas Research. Cite sources and refuse unsourced claims. |
| a3 | Calendar Concierge | Harbor Freight Co. | Active | 6 hours ago | Calendar Management | You are Calendar Concierge. Propose times in the customer’s timezone. |
| a4 | DealDesk Copilot | DealDesk LLC | Paused | 1 day ago | Document Analysis, Calendar Management | You are DealDesk Copilot. Draft deal summaries from approved documents only. |

Skill lists start **collapsed** (`open: false`).

### 6.3 Skills

| id | name | category | description | permission scope | version | updated | enabled on | usage |
|----|------|----------|-------------|------------------|---------|---------|------------|-------|
| s1 | Web Research | Research | Fetch and summarize public web sources with citations. | Network: allowlisted domains | 2.4.0 | Aug 12, 2026 | Nora Finance, Atlas Research | 72% |
| s2 | Document Analysis | Documents | Extract tables and clauses from PDFs and contracts. | Files: customer vault read | 3.1.1 | Aug 28, 2026 | Nora Finance, Atlas Research, DealDesk Copilot | 88% |
| s3 | Calendar Management | Productivity | Read/write events and propose meeting slots. | Calendar: read/write | 1.8.0 | Jul 30, 2026 | Calendar Concierge, DealDesk Copilot | 41% |
| s4 | Invoice Matching | Finance | Match invoice line items to purchase orders. | Finance: invoices read | 1.2.0 | Sep 1, 2026 | Nora Finance | 54% |

Enabled-agent **counts** must match the agent skill lists (2, 3, 2, 1).

### 6.4 Contracts

Itemized math must match **Amount paid**.

**CNT-2041** — Acme Labs / Nora Finance / Active / Mar 1, 2026 – Feb 28, 2027

- Base rental: $2,400
- Document Analysis: $420
- Web Research: $360
- Invoice Matching: $280
- Discount: −$180
- **Amount paid: $3,280**

**CNT-2048** — Northstar Inc. / Atlas Research / Active / Apr 15, 2026 – Apr 14, 2027

- Base rental: $2,100
- Web Research: $360
- Document Analysis: $420
- Discount: $0
- **Amount paid: $2,880**

**CNT-2102** — Harbor Freight Co. / Calendar Concierge / Trial / Jul 12, 2026 – Aug 11, 2026

- Base rental: $490
- Calendar Management: $190
- Discount: −$80
- **Amount paid: $600**

**CNT-2119** — DealDesk LLC / DealDesk Copilot / Completed / Jan 9, 2026 – Jul 8, 2026

- Base rental: $1,800
- Document Analysis: $420
- Calendar Management: $190
- Discount: −$210
- **Amount paid: $2,200**

### 6.5 Errors

| id | time | agent | type | description | state | context |
|----|------|-------|------|-------------|-------|---------|
| e1 | Sep 9, 2026 09:14 | Atlas Research | Critical | Source fetcher aborted after 3 retries | Open | Request `GET https://sources.agenthub.example/v1/ingest` |
| e2 | Sep 9, 2026 08:41 | Nora Finance | Integration | Billing webhook signature rejected | Open | Endpoint `/webhooks/stripe` for invoice `inv_8841` |
| e3 | Sep 8, 2026 21:03 | Calendar Concierge | Timeout | Google Calendar list events exceeded 12s | Open | `calendar.events.list` for `harborfreight.example` |
| e4 | Sep 8, 2026 16:22 | DealDesk Copilot | Permission | Vault read denied for folder `legal/nda` | Open | Skill Document Analysis, role `operator` |
| e5 | Sep 8, 2026 11:05 | Nora Finance | Validation | Invoice total did not match line items | Resolved | Document `PO-4419.pdf`, expected 12840 got 12804 |
| e6 | Sep 7, 2026 19:48 | Atlas Research | Integration | Citation parser received empty HTML | Open | Tool `web_research.fetch`, url host `news.northstar.example` |

Each error has a readable multi-line trace in its detail modal (hardcoded stack-style text is fine).

---

## 7. Data consistency rules

- Nora Finance belongs to Acme Labs and appears in Agent Management, Agent Contracts, and Error Log.
- Atlas Research belongs to Northstar Inc. and appears in Agent Management, Agent Contracts, and Error Log.
- Calendar Concierge and DealDesk Copilot use the same names and owners wherever referenced.
- Contract modal totals equal the visible amount paid after base fee, skill prices, and discount.
- Skill enabled counts correspond to the agent assignments shown in Agent Management.

---

## 8. Interaction details

### Action menus

- Trigger is a `button` labeled for assistive tech (for example `Actions for Nora Finance`).
- Menu items are `button` elements.
- Opening a menu closes any other open menu.
- Clicking outside or activating the trigger again closes the menu.

### Modals

Every modal closes from:

1. Its close (X) button
2. Backdrop click
3. Escape key

Focus should move into the dialog when opened. **View detail** / **Configure** must open a relevant modal in at least four sections (Users, Agents, Skills, Contracts, and Errors all qualify).

### Toasts

Show a short confirmation for Save changes, Delete, and Mark as resolved. Auto-dismiss after a few seconds. Toasts are not a substitute for visible row/card updates.

### Mark as resolved

Updates the error’s resolution state immediately, mutes the row, and disables **Mark as resolved** for that item until refresh.

---

## 9. Acceptance criteria

1. Repository history shows `SPECS.md` committed before AgentHub `index.html` changes.
2. All six sections are reachable from the persistent sidebar and exactly one navigation item has an active indicator.
3. Dashboard displays four complete metric cards and a full-width weekly activity visualization.
4. User Management contains at least five users with working action menus.
5. Agent Management contains at least four agents whose skill lists start collapsed and animate open and closed.
6. Skills contains at least four catalog entries plus an in-panel explanation of skills.
7. Agent Contracts contains at least four contracts and itemized detail modals whose totals are internally consistent.
8. Error Log contains at least six entries with color-coded types or severities.
9. Every list or table record includes a working action dropdown that toggles on trigger click and closes on outside click.
10. View detail or Configure opens a relevant modal in at least four different sections.
11. Every modal closes from a close button, backdrop click, and Escape key.
12. Agent configuration exposes an editable system prompt in a `textarea`.
13. Mark as resolved visibly updates an error entry without reloading the page.
14. The light/dark toggle changes the complete interface and remains selected when navigating between sections.
15. No custom CSS file, inline `style` attribute, framework, jQuery dependency, or build tool is used.
16. Names, skills, owners, financial values, and statuses remain consistent across related views.
17. Semantic HTML and accessible controls are used throughout.
18. The interface remains usable at desktop and tablet widths, including overflow handling for wide tables.

---

## 10. Out of scope

- Authentication, role-based access, and multi-admin accounts
- Real payments, coupon engines, or contract PDFs
- Live agent runtimes, websockets, or log streaming
- Creating new users/agents/skills/contracts from empty forms (delete/save/resolve on existing records is enough)
- Automated tests and CI
