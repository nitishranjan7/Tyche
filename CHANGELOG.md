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
