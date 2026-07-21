# Changelog

A running log of commits to `index.html`, in order, for rollback reference. Each
entry lists what changed and the exact command to revert the working tree to
the state right before that commit shipped.

To roll back to just before a given commit (discarding it and everything after):

```
git reset --hard <commit>~1
git push --force origin main   # only if it was already pushed, and only after confirming with the user
```

To undo a single commit without touching the ones after it:

```
git revert <commit>
```

## Log (most recent first)

Each entry's "revert to" commit is the one immediately before it — i.e. the
state you'd land on with `git reset --hard <revert-to>`.

- `977edbd` — Visually nest sub-teams under their parent instead of equal-size siblings — revert to `c4ead4d`
  `teamsGrid` now lays out parent(+children) "family" blocks
  (`.team-family`) as its grid items instead of every team individually.
  New `teamFamilyHTML()`/`subTeamCardHTML()` replace the old
  `orderedTeamsForDisplay()` flat-list approach. Sub-teams render at
  ~65-70% size (`.compact-sub`, smaller `.sub-dial`) in a `.sub-team-row`
  connected to the parent by a left indent + thin line (`::before` tick),
  replacing the old colored-left-border treatment (which is kept only for
  the case where a sub-team renders alone with no visible parent, e.g. a
  team lead viewing their own sub-team). Families with sub-teams span 2
  grid columns so 2-3 compact cards fit in one row instead of stacking —
  found via an in-browser test with 3 temporary sub-teams that a single-
  column width only fit one compact card per line. Added a mobile
  override (`grid-column:span 1`, column-direction sub-row) after that
  same test revealed `span 2` overflowing the 375px breakpoint. Top-level
  teams with no sub-teams are pixel-identical to before.

- `755639b` — Use white text on solid blue buttons/chips for contrast — revert to `df0497c`
  `.btn.primary`, `.chip.active`, `.seg-btn.active` all had text hardcoded
  to `#1B1508` (a near-black tuned for the old gold background) — left
  unreadable-ish dark-on-blue after the accent swap. Swapped all three to
  `#FFFFFF`; searched the file for `1B1508` first and confirmed those were
  the only three occurrences, so nothing else needed changing.

- `d9389f7` — Switch accent theme from gold/brass to blue — revert to `cab371e`
  `--brass`/`--brass-bright`/`--brass-text` swapped from warm gold to
  steel blue (`#3E6FA6`/`#5B8FC7`/`#2C527D`); `--sage`/`--rust` and their
  `-text` variants left untouched per the request, so success/danger
  states stay visually distinct from the new primary. Logomark `#wheelGrad`
  stops hardcoded to the same three hex values (previously `var(--rust)`
  at 0%, now no rust reference in the gradient at all). Everything wired
  through `var(--brass*)` — active tab border, primary buttons, active
  chip/seg-toggle, the dial's 70-89% "on track" tier, chart bar gradient —
  cascaded automatically, verified in-browser. Left as-is, flagged
  separately: a few hardcoded `rgba(201,154,63,...)` pill/tag backgrounds
  (`.pill.pending`, `.pill.for-approval`, `.pill.for-payment`,
  `.type-tag.transfer`, `.modal-note`) still render the old gold tint
  since they don't reference the variable, while their text color does —
  out of this commit's literal scope, not touched.

- `55389a9` — Merge Bill Payments and Bills into one tab with two internal views — revert to `73bbbd3`
  Removed the separate "Bill Payments" sidebar tab/view. The Bills tab
  now has an internal `.seg-toggle` (reusing the Analytics pattern) with
  "All Bills" (the existing summary/filters/search/list, default active)
  and "Payments" (the former Bill Payments placeholder, now cross-
  referencing "All Bills" instead of "Bills"). New `.bills-subview`/
  `.bills-subview.active` classes and a `setBillsSubview()` toggle mirror
  the existing `.view`/`switchTab()` pattern but operate independently of
  it, so switching sidebar tabs doesn't disturb which sub-view was last
  selected. Role scoping, search, CSV export, and the "+ Add vendor" tile
  all still live inside "All Bills" and are unaffected.

- `1eec1dc` — Increase Tyche wordmark size in sidebar — revert to `14d5e87`
  `.brand` font-size 20px → 25px, so the "Tyche" text reads as a proper
  wordmark next to the 22px logomark instead of an afterthought. Logomark
  SVG dimensions untouched; `.brand` is already hidden at the 760px
  breakpoint so no mobile impact.

- `2e560bd` — Restyle the view-as selector to match the app's design language — revert to `a5a4ead`
  `.select-control` (the Admin/CFO/team-lead selector, and the Analytics
  range selector which shares the class) looked like a raw browser
  `<select>`. Stripped native appearance, added a custom SVG chevron
  (hardcoded to `--text-dim`'s hex since data-URI backgrounds can't read
  CSS custom properties), bumped to font-weight 600 and a fully-rounded
  20px pill radius to match the app's `.chip` language, with brass
  border on hover/focus matching other custom controls. Purely visual —
  underlying `<select>`/`<option>` behavior unchanged.

- `7d4c799` — Shift background palette from tan to neutral white/gray — revert to `030f30b`
  `--ink-2`/`--ink-3`/`--paper`/`--paper-dim` all had a warm beige/tan cast
  (B channel visibly lower than R/G). Replaced with genuinely neutral
  grays where R≈G≈B (`#F7F7F6`, `#ECECEA`, `#EDEDEC`, `#E4E4E2`) — `--ink`
  was already true white and untouched. Cooled `--ink-3` too even though
  the request only named the other three, since it's the hover/active
  surface sitting directly on `--ink-2` cards (tab active state, button
  hovers, pill backgrounds, the budget dial ring) and leaving it warm
  would have produced a visible tan flash on interaction.

- `8a6cad0` — Fix sidebar group label hierarchy — revert to `b26ae9a`
  `.rail-group-label` was reading smaller and dimmer than the tab labels
  beneath it — backwards for a section heading. Bumped to 11.5px/700-
  weight `--text-light` (from 10.5px/600-weight `--text-dim`), so it now
  reads clearly darker and bolder than the 13.5px/500-weight dim tabs
  below it, even though it stays visually compact as an eyebrow label.

- `f11e7d2` — Add team-initiated vendor creation with CFO/Admin approval — revert to `c285389`
  "+ Add vendor" tile (reusing `.add-card-tile`) on the Bills view,
  universally visible so team leads can reach it without needing the
  Admin/CFO-only Vendors tab. The modal's "Submitting team" select is
  pre-filled and locked to the current team lead's team; Admin/CFO get an
  open dropdown across all teams (including sub-teams). New vendors land
  with `status:"Pending approval"` and a linked `state.approvals` entry
  (`type:"vendor"`, `vendorId`). `state.approvals` entries now carry a
  `type` (`"transfer"` for existing/new transfer requests, `"vendor"` for
  these) rendered as a small colored tag on each Approvals row; vendor
  rows show "—" instead of a dollar amount. `decideApproval()` now
  branches on `type` — vendor approvals flip the linked vendor to
  "Active" (or "Rejected", kept visible for audit rather than deleted)
  instead of creating a transaction. An approved vendor is a plain
  `state.vendors` entry with no special flags, so it's immediately usable
  anywhere an existing Active vendor would be.

- `47459d8` — Restructure sidebar with dedicated Payments section — revert to `6894496`
  Regrouped into Overview / Payments / Budgets / Spend / Payments Admin /
  Insights. Added three placeholder views (Card Payments, Bank Transfers,
  Bill Payments) — header, subhead, `.empty-state` note, no logic yet.
  The requested grouping omitted "Ledger" and "Bills" entirely even though
  both are fully-built existing tabs; per user direction, Ledger landed in
  Insights (next to Analytics) and Bills stayed in Payments (next to Bill
  Payments, which explicitly references it). `applyRoleVisibility()` now
  also hides the "Payments Admin" group label for team leads, since its
  only child (Vendors) is already Admin/CFO-only and an orphaned label
  would look broken; Card Payments/Bank Transfers/Bill Payments/Bills stay
  visible to team leads, matching Bills' existing behavior.

- `03a9f11` — Add one level of sub-teams to the data model and UI — revert to `54c2696`
  Teams gained an optional `parentTeamId` (null for top-level). The one-
  level cap is enforced at the data-entry point: `openNewTeamModal()`
  populates the "Parent team" dropdown only with teams that don't already
  have a parent themselves, so a sub-team can never be picked as a parent.
  Teams & Budgets orders sub-teams directly after their parent via a new
  `orderedTeamsForDisplay()` and tags each with a "↳ Sub-team of X" label
  plus a brass left-border/indent treatment. Sub-teams are full `state.
  teams` entries — Transfer, Adjust cap, the dial, and `teamSpent()` all
  treat them exactly like top-level teams, with no rollup into the parent.
  Seeded one demo sub-team ("Growth", under Marketing) so the nesting is
  visible without manual setup.

- `6a9ab3e` — Introduce CFO role distinct from Admin — revert to `47d8cd8`
  Added "CFO" as a second `state.currentView` value (selector order: Admin,
  CFO, then team leads) alongside a new `isCFO()` helper. Every place that
  branched on `isAdmin()` for data/budget visibility (dashboard balance,
  Teams & Budgets, Approvals, Ledger, Expenses, Bills, Cards, Vendors/
  Analytics tab visibility, period controls, add-card tile, CSV export)
  now checks `isAdmin() || isCFO()`. The settings gear and its threshold
  modal stay gated on `isAdmin()` alone — CFO gets full financial
  visibility but not account-level controls. Updated the two on-screen
  strings that referenced "Admin only" to reflect the new parity.

- `835a03a` — Add summary stats and budget comparison to Analytics — revert to `a750bed`
  Four-card `.grid-4` summary above the chart (Total spend for the current
  mode, Vs. last month, Top vendor, Top team) plus a data-driven insight
  sentence ("Spend is up/down X%, driven by [vendor]") — the driver is
  whichever vendor had the largest month-over-month delta, not just the
  12-month leader. "By team" mode now renders two adjacent bars per team
  (muted cap vs colored actual spend, scaled to a shared max so an
  overspending team's bar visibly exceeds its own cap bar) instead of a
  single total-spend bar, with a small legend shown only in that mode.

- `55e66a4` — Add login gate with demo credentials — revert to `07e1837`
  Full-screen `.overlay`/`.modal` (reusing the post-redesign styling)
  active by default, gating the app behind `test-user` / `password123`.
  In-memory only — no localStorage/sessionStorage/cookies — so a real
  refresh always shows it again. `body:not(.authenticated) .app{display:
  none}` hides the app shell, and the bottom-of-script `renderAll()` call
  was removed in favor of calling it only from `attemptLogin()` on
  success, so nothing renders/initializes pre-login. Credentials shown
  in plain muted text under the form since this is a public demo.

- `3abd265` — Sharpen visual design — display type, contrast, corner radius — revert to `dff3b2b`
  Moved away from the generic templated look: imported Space Grotesk and
  swapped it in for `h1`/`.team-name`/`.modal h3` (Fraunces kept only for
  the sidebar wordmark). Pushed contrast — `--ink` to pure white, `--text-
  light` to `#0F1216`, `--line` to `#D8D4CB`, `--ink-2` to a cooler `#F5F4F1`.
  Reduced `border-radius` on cards/buttons/modals from 10-12px to 6px;
  pills stay rounded at 14px, chips/search-input/badge untouched (already
  deliberate pill shapes). Wheel logomark, brass/rust gradient, and budget
  dial left exactly as they were.

- `630a63e` — Reorganize sidebar into usage-based groups — revert to `8c36e80`
  Flat 9-tab list restructured into labeled groups (Overview, Spend,
  Payments, Insights) via a new `.rail-group-label` eyebrow-text class
  matching the existing `.pot-label` pattern. Purely a markup/CSS
  reordering — `applyRoleVisibility()` still targets tabs by `data-tab`
  so Vendors/Analytics hiding for team leads needed no changes. Group
  labels hidden at the 760px breakpoint, same as the brand/rail-footer.
- `d335776` — Add first-run empty state and demo data reset — revert to `5f556fd`
  Small "View empty state" / "Restore demo data" links at the bottom of the
  sidebar. The former clears `teams`/`transactions`/`approvals`/`expenses`/
  `bills`/`vendors`/`cards` and zeroes `totalBalance`; the latter reloads a
  fresh copy of the seed data. Refactored the initial `state` object literal
  into an `initialState()` factory so restore can reconstruct it exactly
  (including `Date` fields, which a JSON round-trip would have broken).
  Added friendly `.empty-state` messages to Teams & Budgets and Cards
  (previously had none), a dedicated $0 empty Dashboard prompt, and made
  the existing Ledger/Bills/Vendors empty messages distinguish "no data at
  all" from "no filter/search match."
- `693d988` — Fix dense list layouts for mobile — revert to `a24d015`
  Also added the missing `<meta name="viewport">` tag — without it mobile
  browsers render at a virtual ~980px viewport and the existing 760px
  breakpoint never actually fires on a real phone. Expenses/Bills/Cards/
  Vendors rows now wrap their fields into `.row-primary` (identity +
  headline figure) and `.row-secondary` (everything else) containers; both
  are `display:contents` by default so desktop is pixel-identical to
  before, and become real stacked flex rows at the breakpoint. `order` is
  set on each field, scoped per view, to preserve the original left-to-
  right column order in both modes. Ledger/Approvals rows were left as-is
  (out of scope, already just 4 columns).
- `cb3976f` — Add explicit single-currency positioning note — revert to `0a61544`
  Small text under the Admin dashboard's total balance card stating Tyche is
  single-currency by design, turning the lack of multi-entity FX support
  into a stated product choice rather than a silent gap. Admin-only since
  it lives inside the admin branch of `renderDashboard`.
- `0ec33db` — Add CSV export to Ledger and Bills — revert to `2cf7ca5`
  Admin-only "Export CSV" button in the header row of each view. Builds the
  CSV client-side from the currently visible (filtered/searched) rows via a
  Blob and a temporary `<a>` — no server round-trip. Refactored the row
  filtering in `renderLedger`/`renderBills` into shared `ledgerRows()` /
  `billRows()` helpers so the export and the on-screen list can't drift.
- `3fea232` — Add search to Ledger, Expenses, Bills, and Vendors — revert to `df4fbca`
  Each view gets a pill-shaped search input (styled like the existing filter
  chips) that does a case-insensitive substring match against the view's
  most relevant text field(s) — Ledger: recipient/description, Expenses:
  merchant, Bills: vendor/description/invoice, Vendors: name/owner. Search
  combines with team scope and status chips rather than replacing them;
  added `state.search` and a shared `setSearch(key, value)` dispatcher.
- `4124f6a` — Complete vendor-bill linkage for all vendors — revert to `8d361f7`
  Added Datadog, AWS, Google, LinkedIn, and Slack to `state.vendors` (owner
  names drawn from existing team members, invented payment details matching
  the existing style). Added `vendorId` to the 5 bills that were still
  missing one, so every bill now links to a vendor.
- `5b404be` — Add mark-as-paid to Bills and card issuance to Cards — revert to `cdb3dd9`
  Bills gets a "Mark as paid" button (advances one pipeline step per click,
  available to both roles). Cards gets a "+ Add card" tile/modal (nickname,
  type, limit, team; defaults $0 spend/Active), Admin-only since its team
  dropdown would otherwise let a team lead assign a card to a team they
  can't see.
- `ebc7212` — Add spend-by-team analytics mode — revert to `3b91fff`
  Third "By team" Analytics toggle, one bar per team. `teamSpent()` gained an
  optional `since` param (defaults to `state.periodStart`) so this mode can
  call it with an all-time override, matching the existing vendor/month modes.
- `650e183` — Link vendors to bills with spend rollup — revert to `f53ce4b`
  Added `vendorId` to bills matching an existing vendor by name (Adobe,
  WeWork, Figma). Vendors view gained Total spent (all linked bills) and
  Last payment (most recent Paid bill's dueDate) columns, "—" when empty.
- `29ae6d6` — Fix budget period filtering for card spend and align terminology to Admin — revert to `3063dde`
  `teamSpent()`'s `cardSum` wasn't gated by `periodStart` like the other sums,
  so card spend survived period resets. Added `lastChargeDate` per card and
  filtered it the same way; also swapped "root owner"/"owner view" copy on
  the Dashboard for "Admin" to match the view-selector terminology.
- `2d56237` — Replace native prompt with styled modal for budget cap adjustment — revert to `ec9c53f`
  `adjustCap()` no longer uses `prompt()`; opens a modal pre-filled with the
  team's current cap instead, matching the existing overlay/field pattern.
- `187803d` — Add budget period tracking with manual period reset — revert to `1fb7d62`
  `state.periodStart` (defaults to the 1st of the month) now gates
  `teamSpent()`'s transaction/expense/bill sums; card spend is unfiltered
  since it has no date field. Admin-only "Start new period" button near the
  Teams & Budgets header resets it to today; "Resets monthly" shown per dial.
- `9b36252` — Add configurable approval threshold via settings modal — revert to `8c6804e`
  Admin-only gear icon next to "View as" opens a modal to edit
  `state.threshold`; saving re-renders the Teams & Budgets subhead and the
  transfer modal's over-threshold warning.
- `0f97523` — Add role-based view selector with real data restrictions — revert to `f6a6418`
  Top-right "View as" dropdown (Admin + dynamic per-team entries) that filters
  Dashboard/Teams & Budgets/Approvals/Ledger/Expenses/Bills/Cards to the
  selected team and hides Vendors/Analytics from the sidebar for team leads.
- `82db120` — Fix brand wordmark spacing and remove split color styling — revert to `127dd04`
  "Ty" and `<span>che</span>` were separate flex items under `.brand`'s
  `display:flex; gap:8px`, so the 8px gap rendered as a visible split in the
  middle of the word. Merged into a single `<span>Tyche</span>` flex item and
  dropped the brass color override so the whole wordmark uses `--text-light`.
- `ceddcfd` — Add wheel logomark to sidebar brand header — revert to `88aba46`
  Adds the gradient wheel SVG mark to the left of "Tyche" in the sidebar brand
  row; `.brand` switched to flex layout to lay the mark and text out inline.
- `88aba46` — Add CHANGELOG with rollback reference for recent commits — revert to `85502f9`
- `85502f9` — Wire Teams & Budgets into Expenses, Bills, and Cards — revert to `dd1487f`
  Tags each expense/bill/card with a `teamId`; team "spent" in Teams & Budgets is
  now computed from transactions + expenses + bills (sent-for-payment/paid) +
  card spend, instead of a hardcoded number.
- `dd1487f` — Add Analytics view — revert to `6332c63`
  Bar chart (vendor/month toggle) + cosmetic date-range dropdown, new sidebar tab.
- `6332c63` — Add Cards view — revert to `545c0cd`
  Summary row (total/company/employee spend) + card table with limit-usage bar.
- `545c0cd` — Add Vendors view — revert to `72505a3`
  Vendor table: name, owner, mock bank payment info, Active/Draft status.
- `72505a3` — Add Bills view — revert to `cdf736d`
  Overdue/due-this-month/due-next-month summary, 5-status filter chips, bill list.
- `cdf736d` — Add Expenses view — revert to `1711f9d`
  Submission-rate stat + expense list (date, card, merchant, amount, status).
- `1711f9d` — Switch to light color theme and fix minor UI issues — revert to `a695b71`
  Dark→light palette, contrast-safe brass/sage/rust text variants, dial hub dot fix.
- `a695b71` — Rename brand from Vaultline to Tyche — revert to `88d7f4e`
- `88d7f4e` — Initial Tyche demo
