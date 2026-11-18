# Finished goods and taproom transfers — how Ollie is used

Decided with Bart on 2026-11-18. QuickBooks side: `bartlanman/QUICKBOOKS` → `taproom-transfers/` and PLAYBOOK §9. SOP doc for the warehouse and Dev/CPA: [Brewery → Taproom Keg Transfers: Setup & SOP](https://claude.ai/artifact/C84R85yLntFUxYpM48V6KS).

## The model
- **Ollie Ops is the inventory system.** QuickBooks only carries the dollar balances Ollie posts (Raw Materials, Packaging Materials, 1300005 W.I.P., 1300900 Finished Goods - Beer). QBO keg/can items are Service items and never move quantities.
- **The walk-in = Inventory → Finished Goods** (Kegs / Cans → SKU → lot). It's created only by **Record Packaging** (or Repackaging / the FG importer).
- **Depletion = Sales page, $0 invoice.** Used for distributors and for the taproom. Don't use Adjustments to move beer out: they post to the Inventory Adjustment (shrink) account.
- **Ollie Order is not owned and not needed** (checked 2026-11-18: Settings → Integrations shows only QuickBooks; the Order login doesn't recognize Bart's email). Ops Sales covers depletion; billing happens in QuickBooks.

## Packaging run (brewer, same day as packaging)
Before recording: batch has the right product; no red "Adjust Inventory" / unassigned lots; the variant has a BOM; no $0 ingredient or packaging lots.
1. Dashboard → tank name ▾ → **Record Packaging**.
2. Product (watch the near-duplicates), variant, units packaged.
3. Packaging date (backdate if late), volume left in tank (0 empties it; loss is calculated), lot code (optional), Tax Determined if from a non-tax-determined tank.
4. + Add container for other formats → Record Packaging.
QBO effect (once reconnected): JE debit Finished Goods, credit W.I.P. + packaging materials.

## Customers used for depletion
| Ollie customer | Use | Notes |
|---|---|---|
| Blue Ridge Beverage, Brown Distributing RVA, Select Wines NOVA, Tri-Cities Beverage Co., Commonwealth Crush, Zymurgy Synergy | Distributor orders | $0 invoices since ~mid-2025; billing is in QBO |
| **Taproom Sales/Transfer** | Kegs/cases pulled for the Waynesboro taproom | Pairs with the QBO invoice to *Taproom - Internal*; the QBO side is closed out daily by automation |
| RVA Basic City | Kegs to RVA | Bask RVA LLC is a separate company: real sales, not internal transfers. The 8 old "overdue" RVA invoices (~$24.0K) are likely real intercompany balances; review with the CPA before marking paid |

## Warehouse routine for a taproom pull
1. Ollie → Sales → New invoice → customer **Taproom Sales/Transfer** → pick each keg/case from its lot → price $0.00 → Save.
2. QuickBooks invoice to **Taproom - Internal**, location DISTRIBUTION, same items and quantities at wholesale, no keg deposit or excise lines, memo = Ollie invoice #.

## QuickBooks connection
- **Disconnected, and on hold by Bart until the inventory cleanup is done (2026-11-18).** Do not click Connect.
- While disconnected, packaging runs and $0 invoices change Ollie only; nothing posts to QBO. Expect a catch-up and a tie-out (Inventory Value report vs QBO inventory accounts) after reconnecting.
- When reconnecting, confirm Finished Goods maps to **1300900 Finished Goods - Beer** (not the unused 1301900 Finished Kegs), and check whether "Automatically sync packaged inventory" (item-based mode) is on. It should stay off, because the QBO beer items are Service items.

## RVA Basic City customer (confirmed 2026-11-18)
RVA Basic City (Bask RVA LLC) is a franchise. Beer goes Basic City -> Zymurgy Synergy (wholesaler) -> RVA, and Zymurgy Synergy pays Basic City. In Ollie keep logging it to customer **RVA Basic City**; in QuickBooks it is invoiced to **Zymurgy Synergy-PO**. It is a real sale, not a taproom transfer. 2026 check: Ollie RVA 188.7 bbl vs QBO Zymurgy 165.3 bbl, so about 23 bbl to reconcile.
