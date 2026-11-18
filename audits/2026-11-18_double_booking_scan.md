# Double-booking scan — 2026-11-18

One-time read-only scan requested by Bart before relying on the Ollie → QBO → taproom-transfer flow. Scope: 2026 QBO wholesale invoices, brewing-vendor bills/expenses (Boortmalt, Victory Packaging, Malteurop, Country Malt, Yakima), inventory accounts, Taproom - Internal, and Ollie Sales invoices. Nothing was changed.

## Cleanup proposed (needs Bart's OK; all in open 2026 period)
| # | Transaction | What's wrong | Proposed fix | Effect |
|---|---|---|---|---|
| 1 | Invoice **20572800**, Blue Ridge Beverage, 2026-03-05, $15,640.00, **open** (id 68176) | Line-for-line duplicate of invoice 20572802 (2026-03-06, $15,640.00, paid 3/16): 400 × 6th Lord 4/6pk, 24 × 6th Lord ½bbl, 8 × ODP ½bbl, 32 keg deposits | Void 20572800 | A/R −$15,640; distribution sales −$14,680; keg deposit liability −$960 |
| 2 | Invoice **20572799**, customer "DATA COLLECTION INVOICE DELETE", 2026-03-05, $2,807.45, **open** (id 68138) | Price-list/test invoice (1 of every SKU) never deleted | Void 20572799 | A/R −$2,807.45; distribution sales −$2,761.36; excise payables −$46.09 |
| 3 | Expense **69050**, Checking, 2026-03-12, $5,876.62, "DR METAL CRAFT BREW PPD", coded to **1301300 PACKAGING MATERIALS** (asset), payee victory packaging | Its reversal (deposit 69051, 2026-03-13, "CREDIT BACK ITEM") is coded to **5301350 CAN PACKAGING**, so the pair doesn't net: asset +$5,876.62, COGS −$5,876.62. This is the entire $5,876.62 sitting on the PACKAGING MATERIALS parent. Same-day Boortmalt bill payment 68710 ($5,876.62 ACH) paid the actual bills; "DR METAL CRAFT BREW PPD" is Boortmalt's debit pattern | Recode 69050's line to 5301350 CAN PACKAGING (optionally payee → Boortmalt) | Packaging Materials parent → $0; COGS +$5,876.62 |

## Review with source documents (no change proposed yet)
4. Victory Packaging bills 70361 ($1,537.11, open) and 70362 ($2,443.22, $258.93 open) share invoice # **268182969**. One doc # is probably mis-keyed. Check Victory's statement.
5. Victory Packaging open A/P **$12,300.48** (6 bills, Feb–Mar 2026, 200+ days overdue) while weekly $2,000 checks (#1249–1254, entered 2026-11-11) were applied. Get a Victory statement and confirm what's really owed.
6. Five $2,839.72 ACH bill payments to Victory (3/18, 3/24, 4/3, 4/8, 4/14). Checking is reconciled through 9/30/26, so they likely match real bank debits (installments). Confirm against the bank if the Victory statement disagrees.
7. Two customer records for RVA: **Bask RVA LLC** (1191) and **Bask RVA LLC (Basic City Beer Co RVA)** (1602). 1191 holds a 2025 open invoice 20473882 ($2,357.02). 2025 is filed → accountant.
8. 2022 QBPOS summary invoices carry penny balances ($0.01–$0.30 each). 2022 is filed → leave or accountant.

## Ollie side (Ollie-only today; matters at reconnect)
9. Taproom Sales/Transfer invoices with prices instead of $0: **#320** $1,216.50 (unpaid), **#317** $126.00. Blue Ridge **#321** $765.00 (unpaid). Set to $0 (or confirm) before reconnecting.
10. The 53 legacy "overdue" Ollie invoices (~$566.5K, Oct 2024–May 2025, from the 2026-11-07 audit) must be closed in Ollie before reconnecting, so nothing pushes A/R or payments into QBO.

## Reconnect risks (decision needed before Ollie ↔ QBO goes back on)
11. **Brewing purchases are expensed straight to COGS in QBO** (Boortmalt → 5001000, Victory → 5301350, Yakima → 5001000 by card). Ollie POs, once reconnected, create Bills to the **inventory asset** accounts, and Ollie later moves that cost to COGS when beer sells. Both running = purchases booked twice and COGS counted twice. Pick one: (a) Ollie owns brewing purchases (bookkeeper stops keying those bills and only pays/matches Ollie's), or (b) keep manual entry and leave Ollie disconnected.
12. **No Ollie postings in 2026.** Raw/WIP/FG balances come from the accountant's 2025-12-31 JEs ("adjust inventory to actual production report"). At reconnect, post one cutover JE so QBO inventory accounts = Ollie Inventory Value on the cutover date (Nov 9 baseline: QBO $276,599 vs Ollie $114,365). Don't let Ollie back-sync into 2025.

## Clean
- No QBO invoices or bills for Taproom - Internal / Basic City Brewing - Internal yet; Inter-Department Clearing $0.
- No other duplicate wholesale invoices in 2026 (103 invoices checked by customer, amount and date).
- No JE activity in Finished Goods or W.I.P. in 2026, so Ollie and QBO can't be double-counting inventory today.
