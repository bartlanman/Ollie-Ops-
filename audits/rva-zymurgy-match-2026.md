# RVA / Zymurgy Synergy match — 2026 (checked 2026-11-18)

Chain: Basic City (QBO realm 1421738425) invoices **Zymurgy Synergy-PO (729)** -> Zymurgy Synergy LLC (QBO realm 9130351439209336) invoices **Bask RVA LLC- ABC#013486625** -> RVA pays Zymurgy by QuickBooks Payments -> Zymurgy pays Basic City. Ollie logs the same beer to customer **RVA Basic City**.

## Invoice-by-invoice (same delivery dates)
| Zymurgy -> RVA | Zymurgy # | Amount | Basic City -> Zymurgy | BCBC # | Amount | RVA price / BCBC price |
|---|---|---|---|---|---|---|
| 2026-01-07 | 1122 | $2,581.25 | 2026-01-08 | 0013080 | $2,099.68 | 1.23 |
| 2026-01-22 | 1123 | $4,238.25 | 2026-01-22 | VRMA32 | $3,397.00 | 1.25 |
| **2026-02-12** | **1124** | **$4,635.50** | **none** | **MISSING** | | |
| 2026-03-04 | 1125 | $4,274.00 | 2026-03-05 | 20572801 | $3,604.75 | 1.19 |
| 2026-03-26 | 1126 | $4,916.50 | 2026-03-26 | 20576378 | $4,048.10 | 1.21 |
| 2026-04-09 | 1127 | $3,280.25 | 2026-04-09 | 20576385 | $2,599.52 | 1.26 |
| 2026-04-23 | 1128 | $3,248.75 | 2026-04-23 | 20576389 | $2,745.40 | 1.18 |
| 2026-05-14 | 1129 | $4,683.00 | 2026-05-14 | 20595614 | $3,547.00 | 1.32 |
| 2026-06-11 | 1130 | $3,985.75 | 2026-06-11 | 20595623 | $3,423.00 | 1.16 |
| 2026-07-02 | 1131 | $3,098.75 | 2026-07-02 | 20605824 | $2,418.50 | 1.28 |
| 2026-07-30 | 1132 | $3,770.50 | 2026-07-30 | 20605834 | $3,844.75 | **0.98** (check) |
| 2026-08-20 | 1133 | $3,710.25 | 2026-08-20 | 20633062 | $3,028.50 | 1.23 |
| 2026-09-03 | 1134 | $3,638.00 | 2026-09-03 | 20633071 | $2,353.50 | **1.55** (check) |
| 2026-09-23 | 1135 | $3,899.00 | 2026-09-24 | 20639167 | $3,092.00 | 1.26 |
| **2026-10-15** | **1136** | **$2,439.25** | **none** | **MISSING** | | |
| 2026-11-05 | 1137 | $2,906.00 | 2026-11-05 | 20672972 | $2,292.50 | 1.27 |
| **Total** | 16 | **$59,305.00** | | 14 | **$42,494.20** | |

## Findings
1. **Two deliveries were never invoiced by Basic City**, and Ollie has them:
   - **Feb 12** (Zymurgy #1124): 17 half bbl (Bask DIPA 3, Baby Bask 3, 6th Lord 3, Daily Pils 3, Crispy 2, Rumbly 2, Citra Lingus 1), 2 Gringo sixtels, 20 12oz cases (Daily Pils 10, Baby Bask 10), 30 16oz cases (Bask 5, Gringo 10, Rumbly 5, Citra Lingus 10). **Ollie Feb RVA = 17 half bbl, 20 12oz, 30 16oz: exact match.** About 13 bbl.
   - **Oct 15** (Zymurgy #1136): 12 half bbl (Daily Pils 5, Mayor of Munich 5, Hornig 2), ~32 12oz cases (Baby Bask 10, Mayor 6/4 5, Tart Up 7, Cactus Garden 7, Golden Child 3). Ollie Oct RVA = 12 half bbl, 22 12oz cases. About 8 bbl.
   - Together these are most of the ~23 bbl Ollie-vs-QBO gap. At Basic City's usual Zymurgy prices they are worth roughly **$3,800 (Feb) + $2,100 (Oct) = ~$5,900 not billed**.
2. Two invoices are priced out of pattern: **7/30** (Basic City billed more than RVA was charged) and **9/3** (Basic City billed much less). Compare lines before changing anything.
3. **Cash:** RVA paid Zymurgy every invoice ($59,305.00, all paid). Zymurgy paid Basic City $30,589.29 in 2026 (1/28 $5,991.18, 3/11 $5,496.68, 4/6 $9,101.43, 9/16 $10,000.00). Basic City A/R from Zymurgy is **$22,613.99**, including two 2025 invoices (20532121 $1,912.00, 20532130 $2,805.90).
4. **Zymurgy's books are one-sided:** they record its sales to RVA and the deposits, but no bills or payments to Basic City, so its QBO shows the beer cost as $0.

Nothing was changed in either QuickBooks company.

## Resolved 2026-11-18
Bart approved invoicing the two missing deliveries at QBO item prices, with the original dates: **#205373799 (id 73682) 2026-02-12 $3,846.00** and **#205373800 (id 73683) 2026-10-15 $2,113.75**. Basic City→Zymurgy 2026 total is now $48,453.95 over 16 invoices, one for each Zymurgy→RVA invoice. Still to do: add the Ollie-missing lines (Feb 2 Gringo sixtels; Oct 7 Cactus Garden 12oz, 3 Golden Child 12oz) to Ollie RVA Basic City.

## Zymurgy -> Basic City cash, 2026 (checked 2026-11-18 against Zymurgy QBO bank feed, account 151 QuickBooks Checking)
| Zymurgy bank date | Amount | Basic City record | Status |
|---|---|---|---|
| (2025-12-10) | $7,133.40 | deposit 66978 was coded QB LOAN; JE ZS-ADJ-2025-12 (73688) + payment 73689 | applied 11/18 |
| 2026-01-28 | $5,991.18 | payment 67805 | applied |
| 2026-03-11 | $5,496.68 | payment 69063 | applied |
| 2026-04-06 | $9,101.43 | payment 69890 | applied |
| **2026-05-12** | **$1,850.00** | **deposit 70552 (5/13, TRANSFER INTUIT CCD METALCRAFT BREW) coded to "cash advance repayment", memo CHANDLER CC; paired with $1,850 Employee Tips card charge 70551** | **not applied to Zymurgy; ask Bart** |
| 2026-08-02 | $6,970.00 | deposit 71530 was QB LOAN; now payment 73687 | applied 11/18 |
| 2026-09-16 | $10,000.00 | payment 72723 | applied |
| 2026-11-08 | $8,474.00 | payments 73684/73685/73686 (11/9) | applied 11/18 |
| **2026 total** | **$47,883.29** | | |

Not counted: "Instant Transfer to card ending 8654" ($1,000 3/18, $1,000 4/1, $5,000 6/16, $1,000 11/17). Zymurgy has booked these as Shareholder Distributions in the past; they did not go to Basic City checking.
Zymurgy own books: every one of these outflows is still uncategorized in its bank feed (88 rows For Review), so its QBO shows no beer cost.
Basic City A/R from Zymurgy after the 11/18 applications: $5,996.34 (7/30 $1,590.09, 10/15 $2,113.75, 11/5 $2,292.50); $4,146.34 if the $1,850 is a Zymurgy payment.
