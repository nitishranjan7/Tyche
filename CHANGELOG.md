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
