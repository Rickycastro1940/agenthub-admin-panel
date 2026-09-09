# Google Stitch prompt (from SPECS.md)

Paste this into [Google Stitch](https://stitch.withgoogle.com). Generated screens are **design guidance only**. Do not ship Stitch HTML. The final prototype must match `SPECS.md`.

Stitch’s public landing page was opened for this project; generation requires a signed-in Google account. Until a Stitch export is attached, use this prompt plus section 3.4 of `SPECS.md` as the visual proposal: navy sidebar, slate canvas, blue primary actions, rounded cards, semantic badges.

---

Desktop **AgentHub** internal admin for an AI agent rental marketplace. Persona: operations staff (Jordan Hale). Light slate canvas, navy sidebar, blue primary actions, Inter, rounded-xl cards, 1px slate borders, subtle shadows. Professional B2B operations dashboard. Device: 1440×900, light mode first (optional dark second screen).

**Sidebar (persistent, navy):** AH mark, “AgentHub”. Nav: Dashboard, User Management, Agent Management, Skills, Agent Contracts, Error Log. Active item is clearly highlighted. Exactly one active item.

**Top bar:** current section title, compact Production environment chip, light/dark theme toggle.

**Dashboard:** four metric cards in a 4-column desktop / 2-column tablet grid — Monthly revenue $48,290, Discount losses $3,180, Active agents 3, Failing agents 1 — each with icon, comparison text, and a distinct accent. Full-width Weekly Activity seven-day bar chart with legend and text summary. Below: Recent Activity list beside Platform Health.

**Users:** summary counts plus a table (avatar/name, email, plan badge, status badge, join date, ⋮ actions). Five customers including Jordan Hale / Acme Labs and Priya Shah / Northstar Inc.

**Agents:** four cards — Nora Finance, Atlas Research, Calendar Concierge, DealDesk Copilot — owner, status, last run, collapsed skill chips with expand control, ⋮ Configure/Delete.

**Skills:** callout explaining reusable capabilities, then four cards (Web Research, Document Analysis, Calendar Management, Invoice Matching) with category, enabled-agent count, usage bar, ⋮ menu.

**Contracts:** table of four rentals with skill chips, term dates, status, amount paid, ⋮ View detail. Itemized pricing in a modal.

**Errors:** six rows with Critical / Integration / Timeout / Permission / Validation badges, timestamps, agent names, ⋮ View detail / Mark as resolved.

**Chrome:** modal with backdrop; toast region; semantic badges (emerald active, amber trial/paused, rose failing/critical). No login screen.
