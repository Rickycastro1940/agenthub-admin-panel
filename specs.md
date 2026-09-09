# AgentHub Admin Panel — Product Specification

This file is the source of truth for the prototype. An implementer should be able to build the panel from this document alone.

**Process**

1. Commit this specification before any AgentHub `index.html` work.
2. After this draft exists, paste `STITCH-PROMPT.md` into [Google Stitch](https://stitch.withgoogle.com) to generate an initial visual proposal.
3. Treat Stitch output as look-and-feel reference only. Manually implement `index.html` so every requirement in this file is satisfied. Do not ship Stitch HTML.

**Deliverable:** one `index.html` at the repository root, based on the [html-hello](https://github.com/4GeeksAcademy/html-hello) template. Serve with `python3 server.py` (Flask static server on port 3000) or any static file server.

---

## 1. Short description

**What AgentHub is.** AgentHub is a SaaS marketplace where companies rent pre-configured AI agents (support, research, scheduling, and similar roles) and equip those agents with reusable skills such as web research, document analysis, and calendar management.

**What this panel is.** This document specifies the **internal admin panel**: a single-page operations console for monitoring platform health, managing customers and rented agents, maintaining the skill catalog, inspecting rental contracts, and investigating execution errors.

**Who the admin user is.** The only persona is an AgentHub operations administrator. Display name **Jordan Hale**, role **Operations** (Super Admin). There is no login screen, no other roles, and no permission switching. The shell shows this user as already signed in, with environment **Production**.

The prototype is a reviewable frontend reference only. Data is hardcoded. Refresh restores seed data except the theme preference.

---

## 2. Tech stack and constraints

| Use | Do not use |
|-----|------------|
| **HTML** — one `index.html` at the repo root, with semantic elements (`header`, `nav`, `main`, `section`, `table`, and a dialog or equivalent) | Extra app HTML pages |
| **Tailwind CSS via CDN** — official Tailwind CDN script in `<head>` | Custom CSS files, `<style>` blocks, inline `style` attributes, CSS build pipelines |
| **Vanilla JavaScript only** — interactivity in a `<script>` inside `index.html` | Frameworks (React, Vue, Svelte, Alpine.js), jQuery, npm, bundlers, UI component libraries |
| **No backend** — hardcoded / in-memory mock data only | REST APIs, databases, real auth, billing processors, server-side business logic |

`python3 server.py` (or any static file server) may serve the files. It must not implement application logic.

Optional: Inter via Google Fonts.

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

Each section below has **at least three specifications**. A spec names a **component**, describes its **contents**, and defines its **behavior**.

### 4.1 Dashboard (`#dashboard`)

1. **Metric card grid.** Four metric cards in a responsive **2×2** grid (two columns from the `sm` breakpoint). Each card contains an icon, a label, and a hardcoded value: Monthly revenue `$48,290`, Discount losses `$3,180`, Active agents `3`, Failing agents `1`. The grid does not wrap to a single column on tablet/desktop.
2. **Metric card chrome.** Cards use a distinct accent color per metric type (blue revenue, amber discounts, emerald active, rose failing), a 1px border, rounded corners, and a subtle shadow. Comparison text under the value is static. Cards stay readable in light and dark themes.
3. **Weekly activity placeholder.** Below the cards, a full-width placeholder `div` with a dashed border and a centered label (`Weekly activity chart`) represents the chart. Inside that frame, seven hardcoded bars (Mon–Sun) using Tailwind height utilities, a legend (Agent runs vs Errors), and an `sr-only` text summary may be shown; no chart library and no inline `style` attributes.
4. **Recent activity and platform health.** Two equal cards sit under the placeholder: a list of three hardcoded activity sentences, and a four-stat health summary (API uptime, open errors, active contracts, failing agents). They stack on small widths and sit side by side from `lg`.

### 4.2 User Management (`#users`)

1. **Summary chips.** Three compact chips above the table show Total, Active, and Trial counts derived from the user list (seed: 5 / 3 / 1). Counts update if a user is deleted in-session.
2. **Users table.** A horizontally scrollable table with at least five rows and columns for avatar+name+company, email, plan badge, status badge, join date, and actions. Plan and status use the shared Badge component.
3. **Row action dropdown.** Each row’s `⋮` button opens a menu with **View detail** and **Delete**. Only one menu is open at a time; a second trigger click or an outside click closes it.
4. **User detail modal / delete.** **View detail** opens a modal with email, company, plan, status, join date, and rented agents. **Delete** removes the row in memory, shows a toast, and does not persist across refresh.

### 4.3 Agent Management (`#agents`)

1. **Agent card.** At least four cards (Nora Finance, Atlas Research, Calendar Concierge, DealDesk Copilot). Contents: initials avatar, name, owner company, last-run text, and a status badge (Active / Failing / Paused). Cards sit in a one-column layout that becomes two columns from `lg`.
2. **Collapsible skill list.** Each card includes a labeled control (`Show N skills` / `Hide skills`). The skill chips start collapsed (`max-height` 0, opacity 0). Expanding animates max-height, opacity, and chevron rotation; collapsing reverses the same animation.
3. **Configure modal.** The card `⋮` menu offers **Configure** and **Delete**. **Configure** opens a modal whose body is a labeled `textarea` of the agent’s system prompt, with **Cancel** and **Save changes**. Save writes in-memory data and shows a toast. **Delete** removes the card for the session.

### 4.4 Skills (`#skills`)

1. **Definition callout.** A full-width callout at the top of the section states that a skill is a reusable capability that can be enabled on one or more rented AI agents. It is informational only (not dismissible).
2. **Skill card.** At least four cards (Web Research, Document Analysis, Calendar Management, Invoice Matching). Contents: name, short description, category badge, enabled-agent count, and a hardcoded usage bar plus percentage. Layout is one column, two columns from `md`.
3. **Skill actions.** Each card’s `⋮` menu contains **View detail** and **Delete**. **View detail** opens a modal with description, category, permission scope, enabled agents, version, and last updated date. **Delete** removes the card in-session and shows a toast.

### 4.5 Agent Contracts (`#contracts`)

1. **Contracts table.** A horizontally scrollable table with at least four rows. Columns: contract id + client, agent name, skill chips, human-readable term dates (`Mar 1, 2026`), status badge, right-aligned amount paid, and actions.
2. **Skill chips and money.** Contracted skills render as compact chips (not a comma string). Currency uses a consistent USD format and stays right-aligned so amounts can be compared down the column.
3. **Itemized detail modal.** Each row’s `⋮` menu contains **View detail**. The modal lists client, agent, term, status, base rental, each skill name with its price, discount, and total paid. The total must equal base + skill prices − discount and must match the table’s amount paid.

### 4.6 Error Log (`#errors`)

1. **Errors table.** At least six hardcoded rows with timestamp, agent name, type badge, short description, resolution state, and actions. Type badges are visually distinct for Critical, Integration, Timeout, Permission, and Validation.
2. **Resolved appearance.** Rows already Resolved (or marked resolved in-session) use muted background and text without dropping contrast below readable. The resolution state badge switches to Resolved immediately after the action.
3. **Error actions.** Each row’s `⋮` menu contains **View detail** and **Mark as resolved**. **View detail** opens a modal with id, agent, timestamp, severity, request/context, and a `<pre>` trace. **Mark as resolved** updates the row without a reload and disables that action for the rest of the session.

---

## 5. Component inventory

Reusable UI components shared across sections. Implement each once and reuse it. Every entry names the component, describes its contents, and defines its behavior.

| Component | Contents | Behavior |
|-----------|----------|----------|
| **Sidebar** | AgentHub `AH` mark, product name, and six nav items (icon + label): Dashboard, User Management, Agent Management, Skills, Agent Contracts, Error Log | Persistent on every view. Selecting an item reveals that section without a page reload, updates the URL hash, and applies a visible active state to exactly one item. |
| **Metric card** | Icon, label, and a hardcoded value (optional comparison text) | Rendered in the Dashboard 2×2 grid. Distinct accent color per metric type, 1px border, rounded corners, subtle shadow. Display only; not clickable. |
| **Action dropdown** | `⋮` trigger `button` and a menu of `button` items (View detail, Configure, Delete, Mark as resolved as required by the section) | One menu open at a time. Trigger click toggles the menu. Clicking outside or clicking the trigger again closes it. |
| **Modal** | Backdrop, title, dynamic body, close control, optional footer actions | Opens from View detail or Configure. Closes from the close button, backdrop click, and Escape. Focus moves into the dialog when opened. |
| **Badge** | Short pill text (plan, status, category, error type, resolution state) | Shared semantic colors: Active / success emerald; Trial / Paused amber; Failing / Suspended / Critical rose; Completed / Resolved muted slate. |
| **Collapsible skill list** | Expand/collapse `button` with chevron, plus skill name chips | Hidden by default on Agent Management cards. Expanding animates max-height, opacity, and chevron rotation; collapsing reverses the same animation. |
| **Dark mode toggle** | Icon `button` in the top bar with an accessible name (for example “Switch to dark theme”) | Toggles the `dark` class on `html` and `body` using Tailwind `dark:` utilities. Persists `light` or `dark` in `localStorage` key `agenthub-theme`. Preference survives section changes. |

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

1. Repository history shows `specs.md` committed before AgentHub `index.html` changes.
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
