# Ollie Ops — Verified UI Notes (live account walkthrough)

Hands-on notes from the real Ollie Ops screens (account 3950107), recorded 2026-11-07.
Forms were opened and cancelled; **nothing was saved**. Where these notes disagree with
`reference/ollie_ops_reference.md` (built from the help center), **trust these notes**.

## Verified page paths
All paths are under `https://app.ollieops.com/3950107/`.

| Sidebar item | Path | Notes |
|---|---|---|
| Dashboard | `dashboard` | Scheduler calendar + tank map/list |
| Tasks | `tasks` | |
| Calendar | `events` | |
| Batches | `batch_logs` | Paginated, 25/page; `?page=N` |
| Barrels | `barrels` | |
| Recipes | `recipes` | |
| Products | `products` | Paginated |
| Raw Materials | `inventories` | Tabs load from `aggregated_inventories/grain`, `/hop`, `/miscellaneous` (+ yeast tab) |
| Packaging Materials | `aggregated_packaging_material_inventories` | Paginated |
| Finished Goods | `finished_goods` | Tabs load from `aggregated_packaged_inventories/keg`, `/can`, `/bottle` |
| Adjustments | `inventory_adjustments` | |
| Sales | `invoices` | |
| Purchase Orders | `purchase_orders` | |
| Reports | `reports` | See report list below |
| Settings | `settings` | Sub-pages below |

Settings sub-pages: `settings/general`, `settings/units`, `members`, `brands`, `ingredients`
(materials), `packaging_materials`, `package_type_setup`, `tanks`, `equipment/brewhouses`,
vendors, `customers`, `custom_fields`, `feature_previews`, `integrations`. Billing lives at
portal.nextglass.co.

Reports (all under `/3950107/`): `inventory_usage_report`, `batch_raw_material_forecast_report`,
`raw_material_report`, `packaging_material_report`, `reports/additional_expenses`,
`purchase_order_report`, `detailed_inventory_on_hand_report`, `inventory_value_report`,
`batch_history_report`, `production_report`, `packaging_report`, `reports/scheduled_packaging`,
`reports/repackaging`, `combined_reporting/systems_report`, `sales/ttb_report/new`,
`production/ttb_report/new`, `sales_report`.

The app is server-rendered: list pages return HTML tables, which makes read-only auditing
straightforward. There is no public API.

## Tank action menu (Dashboard → click tank name ▾)
Eight items for a tank holding a batch:

| Item | What it opens |
|---|---|
| Pitch Yeast | Batch edit page, `#yeast` section |
| Additions | Batch edit page, `#additions` section |
| Transfer | Full page `racking_transactions/new` (needs `from_tank_id` in URL) |
| Record Log Data | Pop-up form (gravity / temp / pH / notes) |
| Record Packaging | Full page `packaging_runs/new` (needs `tank_id`) |
| Update Volume | Pop-up form — see below |
| Dump Remaining | Pop-up — **not opened**; treat as irreversible (moves remaining WIP value out) |
| Tank History | `tanks/:id/history` |

### Update Volume (pop-up) — verified
- Title: "Update volume for <beer>"
- Fields: **Transaction date** (defaults to today), **Current volume** (tooltip: how much beer
  is left in the tank right now), **Notes**, **Track as**.
- **Track as offers only two options: `Sales` or `Loss`** (default `Loss`). The help docs
  describe different options — they're out of date.
- Implication: this form records a *reduction* as either a sale (taproom/serving draws) or a
  loss. It is **not** a neutral "correct the number" tool. Fixing a volume that was entered
  too high (e.g., FV4 at 66 BBL in a 60 BBL tank) through this form would book the difference
  as a loss or sale and move cost out of WIP. Correct over-stated volumes at the source
  (the batch's brew-log volumes) instead — to be verified before any change.
- Buttons: Cancel / Save.

### Transfer (full page) — verified
- Title: "Transfer <beer> from <tank>"
- Fields: **Racking date**, **Volume in <source> after transfer**, **To tank** (search),
  **Volume in to tank after transfer**.
- Right-hand summary shows From / To / **Calculated Losses** live; button "Record Transfer".
- Loss is computed, not entered — the volumes you type determine it.

### Record Packaging (full page) — verified
- Header shows the batch and brew-log numbers (e.g., "BATCH 156-1, 156-2, 156-3 — 6th Lord IPA").
- First required field is **Product**, a dropdown of every product (77 options, including the
  near-duplicates flagged in the audit) — picking the wrong one is easy.

## Still to verify (next walkthrough pass)
- Record Log Data pop-up fields; whether entering FG updates batch ABV immediately.
- Remainder of the packaging form (variant, units, lot code, materials, taxable flag).
- Invoice edit page and what "Mark as paid" does (does it push a payment to QBO?).
- Inventory adjustment form; PO receive form.
- Batch edit page (where brew-log volumes are corrected).
- Dump Remaining confirmation text (open only with approval — may trigger a browser dialog).

## Safety rules learned so far
- Action links in lists (Mark as paid, Post to Accounting, Unpost, Cancel, Discard, Delete,
  complete_task, dismiss notifications) are live actions — never "open" them to look.
- Opening pop-ups and full-page "new" forms is safe as long as Save/Record is not clicked.
- QuickBooks is disconnected as of 2026-11-07; no accounting-affecting change until reconnected.
