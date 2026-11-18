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

## 2026-11-18 (afternoon): RVA deliveries invoiced to Zymurgy Synergy-PO (approved by Bart)
| QBO id | Doc # | Date | Customer | Total | Matches |
|---|---|---|---|---|---|
| 73682 | 205373799 | 2026-02-12 | Zymurgy Synergy-PO (729), loc DISTRIBUTION | $3,846.00 | Zymurgy #1124 to Bask RVA |
| 73683 | 205373800 | 2026-10-15 | Zymurgy Synergy-PO (729), loc DISTRIBUTION | $2,113.75 | Zymurgy #1136 to Bask RVA |

Priced at the QBO item list price (Bart: QB is the source of truth for pricing). Includes lines Zymurgy billed RVA for that Ollie never logged: Feb 2 GRINGO 1/6 BBL; Oct 7 Cactus Garden IPA 4/6pk and 3 Golden Child 4/6. Those need to be added in Ollie.

## Zymurgy Synergy QBO (realm 9130351439209336) changes, 2026-11-18 (approved by Bart: "yes")
- **17 bills to vendor Basic City Beer Co (27), all to Cost of goods sold (24):**
  - Bill 477 BCBC-2025-CARRY dated 2026-01-01, $3,575.68. This is the 2025 beer still unpaid at 12/31/25: $10,709.08 on Basic City's books, less the 12/10/25 $7,133.40 that Zymurgy already expensed in 2025. Zymurgy booked 2025 on payments, so 2025 is untouched.
  - Bills 478-493, one for each 2026 Basic City invoice, with the same date and number, $48,453.95 in all.
- **Bank feed matched as bill payments** (oldest bills first, leaving the same items open as Basic City's A/R):
  - row 217 (1/28 $5,991.18): bill payment 494
  - row 222 (3/11 $5,496.68): bill payment 497
  - row 229 (4/6 $9,101.43): bill payment 499
  - row 243 (8/2 $6,970): bill payment 503
  - row 248 (9/16 $10,000): bill payment 506
  - row 255 (11/8 $8,474): bill payment 514
- **Owner distributions** (Shareholders' equity: Distributions, account 129): card-8654 instant transfers 3/18 $1,000 (510), 4/1 $1,000 (511), 6/16 $5,000 (512), 11/17 $1,000 (513).
- **Fees:** instant-transfer fees 223/227/239/258 posted to Bank fees (517-520); row 221 matched to existing fee record 393.
- **Result:** Zymurgy A/P to Basic City = **$5,996.34** = Basic City A/R from Zymurgy (7/30 $1,590.09, 10/15 $2,113.75, 11/5 $2,292.50).
- **Left pending:**
  - row 234, the 5/12 $1,850 Metalcraft ACH. Waiting on how Basic City books it: deposit 70552 is currently "cash advance repayment / CHANDLER CC".
  - 72 feed rows from 2022-2025, not touched.

## $1,850 resolved, 2026-11-18 (Bart: "1 2 3")
- First Bank x2000 confirms **May 13 TRANSFER INTUIT CCD METALCRAFT BREW +$1,850.00**. It is the only $1,850.00 item Apr 3 - Nov 18; the $1,850.65 items are CNB of WV loan payments.
- Basic City:
  - New **Payment 73691** (Zymurgy, 5/13, $1,850) applied $1,590.09 to 7/30 invoice 70502 and $259.91 to Oct 15 invoice 73683.
  - **Deposit 70552** recoded: its cash-advance line is zeroed and the deposit now carries payment 73691.
  - Zymurgy A/R is **$4,146.34**.
- Zymurgy: feed row 234 matched as **bill payment 521** to bills 488 ($1,590.09) and 492 ($259.91). A/P to Basic City is **$4,146.34**.
- **NOT voided:** Amex/card expense 70551 ($1,850, 5/12, "CHANDLER CC", Employee Tips) is a real bank-feed line, **"ONLINE VSA XXXXX0001 CHARLOTTESVIL VA"**, and was reconciled in the 5/15 card reconciliation (session 416). It stays until Bart says what it was.
- Side effect: account 275 cash advance repayment is now **+$390.00** (was -$1,460). Employee Tips still carries the $1,850 debit from 70551.
