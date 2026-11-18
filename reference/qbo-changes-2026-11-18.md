# QuickBooks changes — 2026-11-18 (inventory / taproom project)

Company: Basic City Beer Co. (QBO realm 1421738425). All dated in open 2026; books closed through 12/31/2025. Every change approved by Bart in chat on 11/18/2026.
Same list as the **QuickBooks Changes** tab in `BCBC_2026_packaging_reconciliation.xlsx`.

## A. Taproom back-fill: 10 journal entries posted (internal transfers, brewery to taproom, at wholesale)
Each: Dr 5700000 COGS - Internal Beer Purchases (location TAPROOM, vendor Basic City Brewing - Internal) / Cr 4700000 Internal Beer Sales - Transfers (location DISTRIBUTION, customer Taproom - Internal). Net $0 to company P&L. Volume = taproom sales from Eve's monthly reports (checked against Toast and Virginia ABC). **Do not back-fill Jan-Oct 2026 again.** November onward = live internal invoices.

| QBO id | Doc # | Date | bbl | Amount |
|---|---|---|---|---|
| 73668 | TRX-2026-01 | 2026-01-31 | 25.34 | $7,050.66 |
| 73669 | TRX-2026-02 | 2026-02-28 | 26.00 | $7,836.26 |
| 73670 | TRX-2026-03 | 2026-03-31 | 31.61 | $9,280.45 |
| 73671 | TRX-2026-04 | 2026-04-30 | 31.75 | $9,600.99 |
| 73672 | TRX-2026-05 | 2026-05-31 | 37.41 | $11,056.38 |
| 73673 | TRX-2026-06 | 2026-06-30 | 28.09 | $8,360.63 |
| 73674 | TRX-2026-07 | 2026-07-31 | 31.79 | $9,419.37 |
| 73675 | TRX-2026-08 | 2026-08-31 | 32.31 | $9,484.16 |
| 73676 | TRX-2026-09 | 2026-09-30 | 29.37 | $8,453.31 |
| 73677 | TRX-2026-10 | 2026-10-31 | 31.91 | $9,049.51 |
| | **Total** | | **305.58** | **$89,591.72** |

## B. Victory Packaging Amex charges recoded (22 expenses on Amex 88008)
Category changed from **1301303 CANS** (inventory asset) to **5301350 CAN PACKAGING** (COGS), to match how Victory checks and bills are coded.

| QBO id | Date | Amount |
|---|---|---|
| 69909 | 2026-06-17 | $141.70 |
| 69907 | 2026-06-17 | $8.62 |
| 69912 | 2026-06-18 | $5,969.57 |
| 70021 | 2026-06-22 | $262.09 |
| 70020 | 2026-06-22 | $1,735.98 |
| 70027 | 2026-06-24 | $322.57 |
| 70045 | 2026-06-29 | $2,429.64 |
| 70327 | 2026-07-08 | $2,971.79 |
| 70424 | 2026-07-22 | $268.18 |
| 70435 | 2026-07-24 | $3,018.27 |
| 70526 | 2026-07-30 | $313.17 |
| 70528 | 2026-07-31 | $1,645.88 |
| 71224 | 2026-09-11 | $5,058.48 |
| 71469 | 2026-09-25 | $1,923.60 |
| 71555 | 2026-09-29 | $24.65 |
| 71553 | 2026-09-29 | $217.89 |
| 72999 | 2026-10-09 | $5,212.17 |
| 73097 | 2026-10-16 | $3,152.79 |
| 73367 | 2026-10-30 | $422.51 |
| 73366 | 2026-10-30 | $36.80 |
| 73646 | 2026-11-10 | $2,577.88 |
| 73655 | 2026-11-13 | $2,049.85 |
| **Total** | | **$39,764.08** |

## C. Double-booking cleanups
| QBO id | Doc # | Date | Change | Amount | Effect |
|---|---|---|---|---|---|
| 68176 | 20572800 | 2026-03-05 | Invoice voided (duplicate of 20572802, Blue Ridge Beverage, paid 3/16) | $15,640.00 | A/R -$15,640.00; distribution sales -$14,680.00; keg deposits -$960.00 |
| 68138 | 20572799 | 2026-03-05 | Invoice voided (test invoice to "DATA COLLECTION INVOICE DELETE") | $2,807.45 | A/R -$2,807.45; distribution sales -$2,761.36; excise -$46.09 |
| 69050 | | 2026-03-12 | Expense (Checking) recoded 1301300 PACKAGING MATERIALS -> 5301350 CAN PACKAGING | $5,876.62 | Packaging Materials parent to $0 |

## D. Setup
- Created **1001020 Inter-Department Clearing** (Bank / cash on hand, id 458) for taproom internal invoices and bills. Always $0.
- Already existed (2026-11-03, not changed): 4700000 (1150040076), 5700000 (1150040077), customer Taproom - Internal (1670), vendor Basic City Brewing - Internal (1671).

## E. Not changed (open)
- 12/31/2025 inventory overstatement (~$127K, Ollie PakTech + yeast errors): waiting on Dwight; 2025 can't be amended, goes through 2026.
- Monthly 2026 inventory entries: waiting on Dwight to say who posts them.
- July 2026 taproom walk-in count: blank on Eve's July sheet.
- Victory open A/P $12,300.48 and duplicate doc # 268182969: need Victory statement.
- RVA: Ollie shows ~23 bbl more to RVA Basic City than QBO invoiced to Zymurgy Synergy in 2026.
