# Cellar capacity from Ollie (2026-11-27)

This is a read-only analysis; nothing in Ollie was changed. It feeds the recap plan: `bartlanman/SWITCHCANE` `evidence/capacity-and-labor-minimums-2026-11-27.md`, the ops workbook's Wholesale Build tab and the Supabase table `plan.capacity`.

## Equipment (Settings → Equipment)

**Brewhouse:** 20 bbl. It is not the constraint.

**Fermenters: 600 bbl in 14 tanks**

| Size | Tanks |
|---|---|
| 20 bbl | FV12, FV13, FV14 |
| 40 bbl | FV1, FV2, FV3, FV7, FV8, FV9 |
| 60 bbl | FV4, FV5, FV10, FV11, Lager Boi |

**Excluded:** the 3.5 bbl prop tank, and the barrels (about 17 bbl).

**Brites: 107 bbl**
- Brite 1 = 40
- Brite 2 = 60
- Brite 3 = 7

## Recipe tank time (recipe pages; days)

| Recipe | Fermentation | Dry hop | Crash | Lagering |
|---|---|---|---|---|
| 6th Lord | 7 | 3 | 3 | 14 |
| Our Daily Pils | 14 | 0 | 7 | 21 |
| Hornig | 14 | 0 | 7 | 14 |
| Bask | 7 | 3 | 3 | 14 |

Weighted by 2026 packaged volume (`reference/BCBC_2026_packaging_reconciliation.xlsx`):
- **Ales:** 71% of barrels, 26.5 days in a fermenter, then about 2 days in a brite.
- **Lagers:** 29% of barrels, 39.5 days in total, including 14 days lagering.

## How the cellar runs (Bart, 2026-11-27; settled, do not ask again)
- Lagers lager about **two weeks in the brites**.
- A lager can **stay in the fermenter** when the brites are full.

## Capacity

The calculation assumes 85% fill and a 2-day turnaround between batches; both are estimates.

| How lagering is split | bbl / yr | What limits it |
|---|---|---|
| All lagering in the brites | about 5,150 | Brites (107 bbl) |
| All lagering in the fermenters | about 5,770 | Fermenters |
| **Best split:** about 32% of lagering days kept in the fermenters | **about 6,300** (5,950 at 80% fill) | Both |
| **Planning value** | **6,100** | |

- **2026 pace:** 2,356 bbl packaged Jan 1–Nov 18, which is about 2,670 bbl a year, or about 44% of capacity.
- **Recap plan barrels (v1.9):** 2029 needs 5,410 bbl (89% of capacity) and 2030 needs 7,286 bbl (119%).
- **What Stage 2 must add by 2030:**
  - about +95 bbl of fermenters (about 693 needed vs 600);
  - about +16–20 bbl of brites (about 123 needed vs 107).
  - Brite 2 is the only brite that takes a full 60 bbl lager batch.

## How the numbers were read
- **Equipment and recipes:** Claude in Chrome. The recipe input fields are hidden from the page reader, so they were read with `fetch` + `DOMParser` (GET only).
- **Batches:** the batch list is at `/batch_logs`; `/batches` returns 404.
