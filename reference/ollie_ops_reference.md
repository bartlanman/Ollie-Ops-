# Ollie Ops Operating Reference (for an AI agent auditing/managing a brewery account)

Target account: https://app.ollieops.com/3950107/dashboard
Source: Ollie Order Knowledge Base, "Ollie Ops" category (support.ollieorder.com), read 2026-11-07. All text below is paraphrased.

> Coverage note. The Zendesk API reported 88 articles in the Ollie Ops category. All were enumerated and nearly all were fetched. About a third of them, including several important ones (Adjustments, Sales, Finished Goods Inventory Management, Raw & Packaging Material Inventory Management, Repackaging, Barrel Actions, Brew Day & Reviewing Batches, Transfers Ops to Order, Ops + Order Overview), are **Loom videos with one line of text and no transcript**. Loom could not be fetched, so for those topics this reference uses what other articles say plus what can be inferred, and marks inferences **[inferred]**. Before doing anything irreversible in those areas, check the actual UI behavior.

---

## 0. TL;DR rules for the agent

1. **QBO sync is one-way: Ops pushes to QBO.** Never "fix" a mismatch by posting JEs in QBO to Ops-mapped accounts. Fix the data in Ops, and Ops will update or delete its own JEs.
2. **Deleting things in Ops deletes or reverses their QBO JEs or Bills.** Brew logs, packaging runs, received POs (via Unreceive) and raw-material lines all behave this way. In a closed accounting period this rewrites history. Get human approval and check the books' close date first.
3. **Price edits after packaging do NOT flow into COGS** for finished goods already packaged with that lot (the QBO article). The Packaging Materials article says a lot price edit updates the FG cost of goods packaged with it, so the two disagree; assume the Ops valuation and the QBO entries can diverge. Fix prices *before* brewing or packaging.
4. **Never brew or package against negative or unassigned inventory.** It is costed at $0 and corrupts COGS. Resolve unassigned lots on the batch or FG first.
5. **Order of operations for reversals:** empty or clear any newer batch out of the tank first, then reverse the transfer or packaging run. You can't reverse anything that has already been sold or removed.
6. **Watch tax-determined double counting.** A tank should not be tax determined if packaging runs from it are also tax determined.
7. Use **Sales** (not Adjustments) to deplete sold finished goods or serving-tank beer. Adjustments post to the Inventory Adjustment (shrink) account, not to COGS.

---

## 1. Navigation map

The left sidebar holds the operational pages. The account/gear/dial icon at the top right holds settings and master data. Only the dashboard URL is confirmed: `app.ollieops.com/{breweryId}/dashboard`, where breweryId is 3950107. Other paths follow `/{breweryId}/<page>` **[inferred; verify in the browser]**.

### Left sidebar
| Page | Probable path | What lives there |
|---|---|---|
| **Dashboard** | `/3950107/dashboard` | Brew scheduler calendar (fermenter rows × dates), the tank map/list view, and per-tank action menus (click the tank name). There is also a Batches tab here for finding scheduled packaging. |
| **Tasks** | `/tasks` | Task list with All / Overdue / Incomplete / Completed filters, plus Calendar and List views. A clipboard icon at the top right shows quick-view tasks that are due or past due. |
| **Batches** | `/batches` | Every batch, with specs, scheduled batches, Racking tab (transfers), Packaging tab (scheduled and recorded runs, Taxable? flag), Fermentation logs, Batch log, Yeast tab, Additions, Cost Breakdown, and Additional Costs. **Import** button for the WIP importer. Pencil icon to edit. |
| **Barrels** | `/barrels` | A separate barrel floor with map/list views. Barrels support the same actions as tanks. |
| **Recipes** | `/recipes` | Recipe list (name plus linked product). New, Import (BeerXML), and a 3-dot menu with Edit/Clone/Delete. The brewhouse is selected inside the recipe. Tabs: Specs, Grains/Fermentables, Mash, Hops, Misc, Yeast, Cost Breakdown (with Projected FG Cost), and Tasks (task templates with offsets). |
| **Inventory → Raw Materials** | `/raw-materials` | Tabs for Fermentables (formerly Grains), Hops, Misc, and Yeast. Filter: In Stock / Below Threshold. Import and New. Click an item to see its lots. Lot menu: Edit / Adjust Inventory / View Transactions. |
| **Inventory → Packaging Materials** | `/packaging-materials` | Same layout as Raw Materials (lots, adjust, transactions, Import, New). |
| **Inventory → Finished Goods** | `/finished-goods` | Grouped Kegs / Bottles / Cans, then by SKU, then lot codes. Import (FG importer). Click a lot code to open its packaging run screen (Materials, Additional Costs, Cost Breakdown, Transactions). |
| **Adjustments** | `/adjustments` | View and create inventory adjustment records for ingredients, packaging, and FG, including backdated counts. |
| **Sales** | `/sales` | Sales invoices that deplete FG or serving-tank volume (for Ops-only accounts and serving tanks). Needs Customers. |
| **Orders (Purchase Orders)** | `/orders` or `/purchase-orders` | PO list with Draft / Open / Received status and a 3-dot menu: Send via Email, Download PDF, Mark Ordered, Edit, Receive, Unreceive, Reorder, Delete. |
| **Reports** | `/reports` | Every report (Section 6), including TTB by Production and TTB by Sales. |
| **Billing** | (Next Glass portal) | Subscriptions, Invoices, Payment details. The portal is portal.nextglass.co and needs Single Sign-Up. |

### Top-right account / gear menu
| Item | Contents |
|---|---|
| **Brewery Settings** | Tabs: **General** (name, EIN, TTB Brewery Number, starting brew-log number, brew-log numbering strategy per batch vs per log, starting PO number, locale, address), **Units** (IBU formula Tinseth/Rager/Haener, volume, gravity Plato/SG, default fermentable unit, default hop unit, temperature, color SRM/EBC), **Integrations** (QuickBooks Online Connect/Configure, Ollie Order), **Equipment** (brewhouses: name, capacity, efficiency %), **Custom Fields**, **Members & Teams**, **Feature Previews** (toggles). A checkbox "make all packaging runs tax determined by default" also lives in brewery settings. |
| **Ingredients** | Ingredient master database with tabs Grains/Fermentables, Hops, Misc, Yeast. Import, New, 3-dot menu (Edit Item / Delete Item / Disable), Bulk Actions → Edit Items (thresholds, QBO asset account override), weight↔volume conversion. |
| **Packaging Materials** (database) | Packaging material master, with the same Import/New/Edit/Delete/Disable/Bulk options and QBO asset account override. |
| **Products** | Products (name, ABV) → Variants (Item, Package type, Unit price, SKU, Bill of Materials). Disable via the 3-dot menu. |
| **Package Types** | Name, capacity, UOM, and default packaging materials inherited by new variants. Disable. |
| **Vendors** | Vendor list. **Sync from QB** button (vendors carry an "i" badge when imported). New, Edit, Disable/Delete. Credit limit is Ops-only. |
| **Customers** | Customers used by Sales. New Customer, Edit, Delete. |
| **Profile** | Name, email, password. |
| Bell icon | Threshold notifications (dismiss one, or dismiss all). |
| Hop Head icon (dashboard) | In-app Resource Center (release notes, walkthroughs, support tickets). |

---

## 2. Core data model

- **Brewery / account.** One Ops account per facility. Multi-facility breweries have several Ops accounts that can transfer materials and FG between them (Transfers Report). An Ops account can be linked to an Ollie Order account; Ollie support does the linking.
- **Brewhouse (Equipment).** Name, capacity in BBL or the configured unit, efficiency %. Efficiency drives the recipe's predicted OG. One brewhouse can have several efficiency profiles. Each recipe is tied to one brewhouse.
- **Recipe.** Name, description, and **Product** link. Specs: target OG/FG/IBU/color, **Pre-boil kettle volume (required, or calculations show 0)**, boil time, **Fermentation length (required; sets the calendar block)**, dry hop/crash/lager lengths, **Brewhouse (required)**. Line items: fermentables (amount, extract FG, color), mash rests, hops (amount, AA, usage, add-at), misc (amount, UOM, added-at, usage), yeast (informational only, **does not deplete inventory**). Also holds task templates. Cost Breakdown projects cost from current inventory prices, and Projected FG Cost per variant by loss %.
- **Product → Variants.** A product is the beer brand (name, ABV). A variant is product × package type, with SKU, unit price, and **BOM** (packaging materials, plus raw materials and additional costs since 4.23.25). The BOM is pulled automatically into packaging runs. Several recipes can map to one product. Variants map to Ollie Order products through Variant Mapping in Ollie Order.
- **Package Type.** Keg/bottle/can size (capacity plus UOM). Default packaging materials are inherited by new variants.
- **Tanks / vessels.** Name, capacity, type (Fermenter, Brite Tank, Serving Tank, Lagering Tank, or Yeast Brink), and **Tax Determined** flag. Only Fermenters appear on the scheduling calendar. Tanks are added in map view with the "+" next to the lock and removed by unlocking and clicking "x".
- **Barrels.** Same model as tanks: name, capacity, tax determined. Bulk import goes through support@getollie.com. Barrel log custom fields record things like angel's share.
- **Batch / Brew Log.** A batch is created when a scheduled calendar block is started ("Start brewing"). It can have several turns, and each turn is its own brew log and its own QBO JE. Brew log numbers follow the numbering strategy. A batch holds the lot number, brew log #, batch ID, volumes (pre-boil and final), ferm logs, yeast pitches, additions (planned and unplanned), racking/transfers, packaging runs, cost breakdown, and additional costs. Finished goods can be added into a batch as an ingredient (6.16.25 release). The value of a batch sits in **WIP**.
- **Raw materials (ingredients).** There are 4 categories: Fermentables, Hops, Misc, Yeast. The master record holds name, UOM, threshold, preferred vendor / amount per unit / purchase UOM / vendor ID number, unit price, an optional QBO asset account override, a weight↔volume conversion (default 1 g = 1 mL), and category-specific attributes (color, extract FG, AA, attenuation, temps). Inventory is held in **lots**: received date, amount, UOM, price per unit, amount per unit, lot code, notes.
- **Packaging materials.** Same structure, master plus lots. Adjustments are always entered in "each".
- **Finished goods.** Lots are created by packaging runs, repackaging runs, or the FG importer. Each lot carries a product variant, a lot code, a unit cost, and a link back to its batch. They are grouped as Kegs, Bottles, Cans.
- **Warehouses.** These live in Ollie Order. Each linked Ops account appears there as an "Ops" warehouse, and FG must be transferred into an Order warehouse before it can be sold. Since 4.23.25, FG can be moved back from Order to Ops.
- **Purchase Orders.** Vendor, invoice number, expected receipt date, and lines (item, amount per unit, UOM, purchase UOM, qty, unit price). Additional costs (freight, tax) are allocated by Fixed, Weight, Volume, or Price. Statuses are Draft → Open (Ordered) → Received.
- **Vendors.** These should be synced from QBO when integrated.
- **Customers.** Used by Ops Sales.
- **Adjustments.** Manual add/remove transactions on a lot (raw, packaging, or FG) and tank volume updates (Normal / Loss / Destroyed on-premises / Dump remaining).
- **Sales.** Ops sales invoices that deplete FG lots or serving-tank volume and post COGS.
- **Yeast pitches.** Each pitch belongs to a tank or batch, with amount, UOM, generation, cost, and custom fields (Pitch Rate by default). Pitches can come from inventory, a previous pitch, or a brink, and can be moved cone→cone or cone↔brink.
- **Tasks / Teams.** Task: due date, description, assignments (users, batches, tanks/barrels). Recipe task templates (name, description, assignee user or team, offset in days from brew day) auto-generate on brew. Deleting a batch deletes its tasks.
- **Custom fields.** Entities: Fermentation Log, Barrel Log, Yeast Pitch. Types: short text, long text, number. "Delete" only deactivates a field; old data is kept.

---

## 3. Workflows (UI step by step)

### 3.1 Initial setup order (implied by dependencies)
Brewery Settings (units, TTB number, brewhouses) → **Vendors (sync from QB)** → Ingredients and packaging materials database (import templates) → Package Types → Products & Variants with BOMs → Customers (if using Sales) → import starting inventory (raw, packaging, FG, WIP) → **only then** configure QBO. Configuring QBO before inventory is loaded produces wrong account values.

### 3.2 Scheduling a brew (Dashboard)
1. Click the calendar cell for the fermenter and date, then choose a recipe. Optionally change the length or the number of turns.
2. Drag the block to move it (only possible before it is brewed). Drag the right edge to change its length. Click the block → **Unschedule** to remove it.
3. Colors: green means scheduled or turns remaining, blue means brewed, yellow means incomplete task, light green means task complete.
Scheduled batches feed the Raw Material Forecast.

### 3.3 Brew day
1. Click the batch block → check the brew date → **Start brewing**. This creates the batch and its brew log.
2. The brew log has tabs: **Mash** (fermentables and mash-stage misc, merged from the old Grain and Mash tabs), **Boil** (hops, boil misc, and **Final Volume**, the knockout volume sent to the fermenter), and **Yeast**.
3. Ingredients in the Mash and Boil stages deplete inventory when the batch starts. Post-boil items (dry hops, fruit) are entered as **Additions** on the batch page on the day they go in, unless the recipe allocates them to the Boil.
4. **Assign unassigned inventory.** If a material has no lot (shown as the red "Adjust Inventory" link), open the row's 3-dot menu → Edit → choose a **Select Lot** value.
5. If the target tank already holds an unpitched yeast pitch, the prompt asks whether to use it. Answering No means you must remove or transfer that pitch from the tank yourself.
6. QBO effect: a JE dated on the brew day credits each raw material's GL account and debits WIP, one JE per turn.

### 3.4 Additions
On the batch page, add **planned** additions (scheduled on a day, for example a recipe dry hop that has no lot yet, so a lot must be assigned) or **unplanned** additions. Each addition depletes the chosen lot and adds its cost to WIP **[inferred: same JE pattern as brew-log materials]**. Finished goods can also be added to a batch as an ingredient.

### 3.5 Fermentation logging
Dashboard → click the tank name → **Record Log Data** → log date, gravity, temperature, pH, plus any custom fields → Save. The entry shows on the batch detail page. Logging updates terminal gravity, then ABV and attenuation. Barrels use **Record Barrel Log Data**.

### 3.6 Yeast
- **Pitch.** Brew log or batch → Yeast tab → **Pitch Yeast** → choose a source (Inventory, Previous pitch, or brink) → amount, UOM, generation (auto-increments and can be edited), cost to carry over (only for a previous pitch), custom fields → Save.
- **Edit a pitch.** Use the pencil icon (amount, UOM, generation, fields).
- **Adjust Cost or Delete.** Only possible before that yeast has been pitched into another batch. A negative adjustment sends cost back to the previous pitch. A delete returns any transferred cost.
- **Move yeast.** Cone→cone or cone↔brink creates a new pitch, with a **Dump Remaining** checkbox. A move does NOT reduce volume at the source; you have to dump it. Recommended: move the cost with the yeast into a brink, then carry it into the next batch.

### 3.7 Transfers, racking, splitting, blending
Dashboard tank menu (Transfer) or Batch → **Racking** tab. A transfer moves a batch tank→tank or tank→barrel, splits it into several tanks, or blends it into an occupied tank. **You can't split and blend in one step**: split into an empty tank first, then blend. Losses raise cost per BBL. There is no QBO JE; the value stays in WIP. Reversing a transfer is blocked while the source tank holds another batch.

### 3.8 Volume updates (losses, dumps, taproom pulls)
Tank name → **Update Volume** → transaction date, the *new remaining* volume, notes, and a classification for the removed beer:
- **Normal**: removal for consumption on TTB. Credits WIP and debits COGS.
- **Loss**: unintended loss. No QBO change; cost per BBL rises.
- **Destroyed on-premises**: credits WIP and debits Inventory Adjustment.
- **Dump Remaining**: credits WIP and debits Inventory Adjustment.
Brew-day volume fixes are made in the brew log (Boil tab → Final Volume).

### 3.9 Packaging
- **Schedule.** Batches → batch → Packaging tab (or the 3-dot menu) → **Schedule Packaging** → date, projected tank, container type, amount, + Add Container → Schedule Packaging. These runs appear on the Scheduled Packaging Report and the Packaging Material Forecast.
- **Record.** Dashboard tank menu → **Record Packaging** (or Batches tab → batch → Packaging → ⋮ next to the scheduled container → Record Packaging) → Product/variant, packaging date (defaults to today; can be backdated), **volume left in tank** (enter 0 to dump the remainder; loss is calculated automatically), amount packaged, lot code (optional, generated if blank), and **Tax Determined** (tick it when packaging from a non-tax-determined tank or unitank) → + Add container for more formats → Record Packaging. Remaining volume can't go negative. The BOM materials (packaging, raw, additional costs) are pulled automatically, and extra materials or costs can be added on the run.
- **Completing / emptying the tank.** Recording packaging with volume left = 0 empties the tank and frees it for the next batch **[inferred: there is no separate "close batch" article; a batch is effectively done when its volume reaches 0 through packaging, sales, or Dump Remaining]**. A batch still showing volume in a tank that is physically empty is a stale batch (see Section 5).
- **Edit.** The packaged-at date and the tax status are edited inline and autosave. To change volumes or amounts, delete the run (the "X" on the batch Packaging tab) and record it again. Deleting is allowed only if the tank is empty or still holds that same batch.
- **Repackaging.** Converts FG lots into another variant or product, for example kegs→cans or a mix pack (multi-product repackaging since 6.16.25). It appears on the Repackaging Report. It can't be reversed after the new item has been sold or removed.
- QBO effect: see Section 4.

### 3.10 Finished goods
- View them under Inventory → Finished Goods (kegs/bottles/cans → SKU → lot).
- **Import** existing FG with the FG importer CSV. Importing does NOT deplete packaging materials. To deplete them afterwards, click the lot → packaging-run screen → Materials → New.
- Adjust from the lot (Adjust Inventory, View Transactions). The Adjustments page also covers this.
- **Transfer to Ollie Order** (to sell through Order). There is no JE, and the FG GL account must match on both sides. Unmapped variants can't move cleanly, so map variants in Order → Settings → Warehouses → Ops warehouse → Variant Mapping. FG can be moved back from Order to Ops (4.23.25).
- Transfer FG or materials between Ops accounts (multi-facility), shown on the Transfers Report.

### 3.11 Raw and packaging inventory
- **Add.** The importer template is preferred. Otherwise use Inventory → Raw/Packaging Materials → New: item, received date, amount, UOM, price per unit, amount per unit, lot code (enter the manufacturer's code), notes. Using **POs is the recommended path when QBO is linked**, because POs create Bills.
- **Edit a lot.** Item → lot ⋮ → Edit. Changing the price changes the cost of FG made from that lot in Ops, but not COGS already synced to QBO.
- **Adjust.** Lot ⋮ → **Adjust Inventory** → Add or Remove, amount (packaging is entered in "each"), notes. This posts to the Inventory Adjustment account.
- **Transactions.** Lot ⋮ → View Transactions. Only the date and notes can be edited. **System-generated transactions can't be deleted**; manual adjustments can.
- **Delete an ingredient or packaging material master.** This is blocked when the item is used on a recipe or BOM or has stock. Disable it instead.

### 3.12 Purchase orders (receiving)
1. Orders → **New** → vendor, invoice #, expected receipt date → + Add new item lines → Save Order (Draft).
2. ⋮ → **Mark Ordered** (Open). On QBO Plus this creates a QBO PO. ⋮ → Send via Email or Download PDF.
3. While the PO is Open: ⋮ → Edit → **Add additional cost** (type, cost, distribution, allocation Fixed/Weight/Volume/Price; weight and volume allocation need matching units) → Save.
4. ⋮ → **Receive** → arrival date (must be after the PO date) → **Update Now** to set lot codes → Update lot codes. Items appear in inventory at **landed cost**. QBO creates a Bill (crediting AP and debiting the mapped asset accounts) dated on the received date.
5. **Unreceive** is possible only if none of the items have been used in a batch or FG. It deletes the QBO Bill, reopens the PO, and removes the inventory value. If items were used, make a manual adjustment instead.
6. **Delete** is possible only for Draft or Open POs and also deletes the PO in QBO. **Reorder** clones the PO into a new Draft.
7. Shortcut: Reports → Raw Material Forecast → **Generate Purchase Orders** creates Draft POs grouped by vendor.

### 3.13 Sales / depletions (Ops-only or serving tanks)
Sales page → create a sales invoice for a customer → choose FG lots or serving-tank volume → save. This depletes inventory and posts COGS (credit FG or WIP, debit COGS) **[inferred from the overview: "so COGS posts to a Cost of Goods Sold account"]**. It feeds TTB by Sales and the Sales Report, which needs invoices enabled. When Ollie Order is in use, sell through Order instead; COGS posts when the Order is **Posted** and lot codes are tracked.

### 3.14 TTB reporting
1. Decide where tax is determined: at the **tank** (tax-determined tanks or barrels; volume entering them is taxed) or at **packaging** (the Tax Determined box on each run, or the brewery-settings default). **Never both for the same beer.**
2. Check each batch: Batches → batch → Packaging tab → the **Taxable?** box. Checked means the volume is included.
3. Reports → **TTB by Production** (Line 10 = packaged BBL; live data) or **TTB by Sales** (Line 10 = sold BBL; combines Ops and Order FG and sales; data is a day old) → select the quarter → review → **Save and create PDF**.
4. Brewery Settings → General must hold the EIN and TTB Brewery Number, which print in the report header.
5. Volume-update classifications (Normal / Loss / Destroyed) feed the removal and loss lines. Production Report columns: Produced / Taxable / Loss volume.

### 3.15 Tasks
Tasks → New → due date, description, assignments → Save. Edit or Delete through the ⋮ menu. Recipe-level task templates with offsets auto-generate when a batch is brewed (one set per batch even with several turns). Teams are set up in Brewery Settings → Members & Teams.

### 3.16 Users
Brewery Settings → Members & Teams → name, email, role → Send Invitation. Owners and Admins can edit or revoke members. The KB does not document the permissions of each role. Login is Next Glass Single Sign-Up, with optional Google sign-in (MFA is available through Google).

---

## 4. Accounting / costing and QBO sync

**Flow:** Raw Materials asset → WIP asset → Finished Goods asset → COGS. Ops pushes Bills, JEs, POs, and (from Order) invoices to QBO. Nothing comes back, except vendor sync (QB → Ops) and, in item-based mode, QBO sales adjusting Ops FG.

### Account mappings (Brewery Settings → Integrations → Configure QBO)
Raw Materials asset (default; can be overridden per category or item), Packaging Materials asset (overridable per item), WIP asset, Finished Goods asset (**must equal the Order FG account**), COGS (**must equal the Order COGS account**), Other COGS (receives "Additional Costs"; required even if unused), Inventory Adjustment (shrink expense), and Accounts Payable (QBO Plus or higher). Optional: "Automatically sync packaged inventory" (item-based: each variant becomes a QBO inventory item). Final step: review balances. A checkbox there will **remove previously synced JEs**; leave it unchecked if corrective JEs have already been booked.
Overrides: Settings → Ingredients (or Packaging Materials) → select → Bulk Actions → Edit Items → QuickBooks Asset Account → Update. The KB does not say whether history is reclassified; assume it is not.

### What each action posts
| Ops action | QBO effect |
|---|---|
| PO → Open | QBO PO (Plus only, informational; unit prices without extra costs) |
| PO → Received | Bill at landed cost, dated on the received date. Credits AP, debits the mapped asset accounts. The memo explains the difference from the PO. |
| **Unreceive PO** | **Deletes the Bill**, reopens the QBO PO, removes the asset value |
| Delete Open PO | Deletes the QBO PO |
| Brew (each turn) | JE: credit each raw material's GL, debit WIP. Memo has product, lot, brew log #, batch ID. |
| Add or remove a material on a brew log | Adds a credit line, or a reversing debit line, and changes the WIP debit |
| **Delete batch** | **The whole brew JE is deleted.** Materials return to their asset accounts and WIP is removed. The batch's tasks are deleted too. |
| Yeast: fresh pitch from inventory or from a brink | JE: credit Yeast GL, debit WIP |
| Yeast: pitch from another batch, or dump from an active batch | No JE |
| Yeast: active batch → brink or open fermenter | JE: debit Yeast GL, credit WIP |
| Yeast: dump a pitch not tied to a batch | JE: credit Yeast GL, debit Inventory Adjustment |
| Transfers, splits, blends, barrel fills | No JE. Losses stay in WIP and raise cost per BBL. |
| Update Volume: Normal | Credit WIP, debit COGS |
| Update Volume: Loss | None |
| Update Volume: Destroyed or Dump Remaining | Credit WIP, debit Inventory Adjustment |
| Packaging run | JE dated on the packaging day: credit each packaging material GL, credit WIP (batch cost line, plus a packaging-loss line if there is loss), debit FG. Additional costs go to Other COGS **[mapping description]**. |
| Add or remove a material on a run | Adds a credit line, or a reversing debit, and changes the FG debit |
| **Delete packaging run** | **The whole JE is deleted.** Values return to packaging, WIP, and FG. |
| Raw, packaging, or FG adjustment | Offset goes to the Inventory Adjustment account |
| FG transfer Ops → Order | No JE (accounts must match) |
| Order: Draft→Accepted (if "Sync non-posted orders" = Yes) or Posted (if No) | Invoice is created, and later edits sync within about 5 minutes |
| **Order Posted** | COGS JE: credit FG, debit COGS, one pair per line ("Product – Size (Lot)"). Needs Order → Settings → Brands → Inventory Tracking → "Track Lot codes and Cost of Goods". Don't Post until lots are correct. |
| Unpost, edit, repost | Invoice updates. The KB does not describe how the COGS JE is handled (verify). |
| Ops Sales invoice | Depletes FG and posts COGS **[inferred]** |

Order invoices also need four mappings: Income service, Government deposit service, Payment deposit account, Keg deposit service. If any is missing, **no orders sync**. Check each order's Integrations tab: Success / "Order Sent", and Success / "Journal Entry Sent" with "Posted".
PO sync icons: grey circular arrows mean the PO synced, black mean the Bill synced.

### Dangerous or irreversible actions (need approval)
- **Unreceive PO**: deletes a QBO Bill. Not possible once items are used.
- **Delete batch / brew log**: deletes the brew JE, its tasks, and its history. Packaging, transfers, and yeast tied to the batch block it or cascade **[verify]**.
- **Delete packaging run or repackaging**: deletes its JE. Blocked once FG is sold or removed, or when the tank holds another batch.
- **Reverse a transfer**: blocked while the source tank holds another batch.
- **Delete PO** (Draft or Open): removes the QBO PO.
- **Editing a lot price** after use: Ops and QBO COGS diverge.
- **Inventory adjustments**: post to the shrink account. Use Sales or Order for real sales.
- **Update Volume / Dump Remaining**: removes WIP value to COGS or Adjustment and affects TTB.
- **Toggling tax-determined** on tanks or runs: changes TTB history.
- **The QBO setup checkbox "remove existing journal entries"**: rewrites QBO balances.
- **Changing QBO account mappings or overrides** after activity: splits balances across accounts.
- **Disconnecting or reconnecting QBO.**
- **Deleting vendors** in Ops (they should be managed in QBO). Deleting ingredients or materials is blocked when in use; use Disable.
- **Removing a tank** from the floor plan, **deleting custom fields** (they become inactive), **deleting tasks**.
- **Moving FG Order → Ops**, and posting or unposting Orders (in the Order app).
- Any change dated in a **closed QBO period**.

---

## 5. Data hygiene: problems, detection, fixes

| Problem | How to detect | Fix | Risk |
|---|---|---|---|
| **Unassigned inventory** (a material on a batch or FG with no lot) | Red "Adjust Inventory" link on the batch or FG; "Unassigned Inventory" error; Raw Material Forecast filter "Negative/unassigned" | Batch or FG → Adjust Inventory → row ⋮ → Edit → Select Lot | Low; updates the JE |
| **Negative inventory** (raw, packaging, FG) | Negative on-hand on the inventory pages or the Inventory on Hand report; "Negative Inventory" error | Receive the missing PO, or open the lot → Adjust Inventory → Add up to 0. Better: add the correct lot, then reassign the batch line. **Fix before packaging.** | Medium; $0-costed usage corrupts COGS |
| **Stale batches in tanks** (the dashboard shows beer in a tank that is physically empty or already packaged) | Dashboard tanks with old brew dates or small residual volumes; the calendar shows batches long past their fermentation length | If it was really packaged: record the missing packaging run. If dumped: Update Volume (Destroyed) or Dump Remaining. If sold from a serving tank: Sales. Leftover volume can be recorded on the last run with "volume left = 0". | Each option posts differently to QBO and TTB; pick the one that matches what actually happened |
| **Scheduled but never brewed batches** | Green blocks dated in the past | Unschedule them, or start brewing with the correct date | Low (no JE before brewing) |
| **Incomplete ingredient cost (red)** | Recipe or batch warning; Projected FG Cost blank | Settings → Ingredient or packaging material → Edit → unit price; also fix the lot prices of received inventory | Changing a lot price after packaging does not fix COGS already synced |
| **Missing BOM (yellow)** | "Missing Bill of Materials" warning; Projected FG Cost inaccurate | Click "Set Now" → variant → Edit → New → add materials | Low; affects future runs only |
| **Missing lot costs / $0 lots** | Inventory Value report lines at $0; lots with blank price | Edit the lot price *before* use; after use, consult the accountant | Medium |
| **Duplicate materials** (often from BeerXML imports with different spellings) | Near-duplicate names in the Ingredients tabs; recipes pointing at items with no stock | Edit the recipes to point at the canonical item, move stock by adjustment (remove/add), then **Disable** the duplicate (it can't be deleted while used or stocked) | Adjustments post to shrink both ways, so the net is $0 when the cost matches; use the same unit cost |
| **Unmapped products / variants** (Ops↔Order) | Order → Settings → Warehouses → Ops warehouse → Variant Mapping blanks; transfers failing | Map each Order product to its Ops product, per Ops warehouse | Low |
| **Unmapped QBO items** | Orders not syncing (missing any of the 4 invoice mappings); "Object Not Found" (an item on a PO deactivated in Ops or deleted in QBO); "Business Validation Error" (vendor name or mapping mismatch) | Fix the mappings and vendors (sync from QB), re-activate the item | Low |
| **QBO disconnected** | "QuickBooks Disconnected" error | Settings → Integrations → Configure QBO → reconnect; contact support if it fails | Actions taken while disconnected may not sync |
| **Ops vs QBO balance drift** | Compare the Inventory Value report (as of a date) with the QBO balances of the RM, Packaging, WIP, and FG accounts | Find the cause: manual QBO JEs to mapped accounts, price edits after packaging, $0 negative usage, imports before QBO was set up | High; don't post plugs without the accountant |
| **Vendors added manually while QBO is linked** | Vendors without the "i" badge | Create them in QBO, sync from QB, and repoint the POs | Medium |
| **POs stuck in Open** (goods arrived but were never received) | Old Open POs | Receive with the correct arrival date | Creates a Bill dated on the receipt date |
| **POs received in error** | — | Unreceive if not yet used; otherwise adjust | Deletes the Bill |
| **Tax-determined double count** | Tank flagged tax determined and runs from it also flagged Taxable | Untick one, per your TTB method | Affects filed quarters |
| **Untaxed packaging** | Packaging tab "Taxable?" empty on runs from non-tax-determined tanks | Tick Tax Determined (it autosaves) | Affects TTB |
| **Yeast still sitting in tanks or brinks** | Tank shows a yeast pitch with no batch | Pitch it into a batch or dump it (dumping posts to Inventory Adjustment) | Low |
| **Wrong UOM conversions** | Hop extract or other cross-UOM usage looks off | Ingredient Edit → weight↔volume conversion (default 1 g = 1 mL) | Low (future use) |
| **Thresholds not set** | Few or no bell notifications; Below Threshold filter empty | Bulk Actions → Edit Items → threshold | None |
| **FG imported without depleting packaging** | Packaging inventory too high | FG lot → packaging run screen → Materials → New | Posts packaging to FG |
| **Stale or overdue tasks** | Tasks → Overdue filter | Complete or delete them | None |

---

## 6. Reports (Reports page in the left sidebar)

| Report | Shows |
|---|---|
| **Raw Material Forecast** | Material needs up to an end date (default 30 days). Batches: All or Scheduled. Materials: All, Below threshold, or Negative/unassigned. Export to CSV. **Generate Purchase Orders** (Drafts by vendor). |
| **Raw Material Forecast by Batch** | Each upcoming batch's shortfalls, with the batch start dates |
| **Packaging Material Forecast** | Packaging needs for scheduled packaging runs. Ending amount filter (Below). |
| **Inventory Usage** | Fermentables, hops, misc, and packaging consumed into batches over a date range |
| **Inventory on Hand** | Quantities as of an end-of-day date, filterable by Raw, Packaging, or FG. Exportable. |
| **Inventory Value** | Dollar value as of a date for grains, hops, misc, yeast, packaging, FG, and WIP. Exportable. **Use it to reconcile against QBO asset accounts.** |
| **Batch History** | Graph of ABV, attenuation, OG, and TG across past batches of a recipe |
| **Production Report** | Over a date range, for all or chosen batches: product, ABV, batch name and #, produced volume, taxable volume, loss volume. Exportable. |
| **Packaging Report** | Product, lot, date, ABV, variant, units, volume, loss, unit cost, total cost |
| **Scheduled Packaging Report** | Date, product, batch, tank, ABV, variant, scheduled units and volume |
| **Repackaging Report** | Date, new and old product, lot, ABV, variant, units, volume |
| **Sales Report** | Ops sales over a period (needs invoices enabled). Exportable. |
| **TTB by Production** | Brewer's Report of Operations, Line 10 = packaged BBL. Live. Quarter selection, then PDF. |
| **TTB by Sales** | Line 10 = sold BBL, using Ops and Order FG and sales plus Ops serving-tank sales. Data is a day old. |
| **Ops x Order Data** | Production, packaging, and sales side by side across Ops and Order (useful for state reporting). Exportable. Data is a day old. |
| **Transfers Report** | Item transfers between facilities or Ops accounts |
| (Ollie Order side) | Inventory Adjustments and Inventory Reconciliation reports |

Also: the Batch detail **Cost Breakdown** tab, and the Recipe **Cost Breakdown** with Projected FG Cost by variant and loss %.

---

## 7. Chrome extension / browser add-on

**None exists.** No KB article, release note, or search result mentions an Ollie Ops Chrome extension, browser add-on, public API, or Zapier-style connector. A search of the help center for "extension" returned 0 results. The Ollie Security article says nothing about browsers or extensions. Ops's only integrations are QuickBooks Online and Ollie Order (plus Next Glass SSO and Google sign-in). Any automation therefore has to drive the web UI (app.ollieops.com) through a browser-automation tool, or work from CSV exports and imports (the Raw Materials, Packaging, FG, WIP batch, Ingredient, Packaging-material, and BeerXML importers; report CSV exports).

Other notes: the new **"Spirits in Ollie" / Distilling** section (DSP features: cookers and stills, proof gallons, bulk spirits in bond, Part VI tags, §5010) applies only when distilling is enabled on a brand. It is out of scope for a brewery-only account. Its Part VI article is still marked as a draft.

---

## 8. Articles read (URLs)

**Ollie Ops category (enumerated through the Zendesk API; 88 articles)**
- https://support.ollieorder.com/hc/en-us/categories/6313315412884-Ollie-Ops (index)
- https://support.ollieorder.com/hc/en-us/articles/37229005760020-How-do-I-find-out-about-New-Releases
- https://support.ollieorder.com/hc/en-us/articles/35958628452372-Ollie-and-QuickBooks-Online-Deep-Dive
- https://support.ollieorder.com/hc/en-us/articles/34292102602900-Updated-Brew-Day-Layout
- https://support.ollieorder.com/hc/en-us/articles/32780143916564-Common-Ollie-Ops-Errors
- https://support.ollieorder.com/hc/en-us/articles/29838922383380-Ollie-Ops-Product-Updates
- https://support.ollieorder.com/hc/en-us/articles/29838262296340-How-Do-I-Update-ACH-as-my-Billing-Method
- https://support.ollieorder.com/hc/en-us/articles/29755329691796-Inventory-Usage
- https://support.ollieorder.com/hc/en-us/articles/16385144344468-How-to-Schedule-a-Packaging-Run
- https://support.ollieorder.com/hc/en-us/articles/14559216841236-How-to-Configure-Quickbooks-in-Ollie-Ops
- https://support.ollieorder.com/hc/en-us/articles/13641599472660-Actions-in-Ollie-Ops-that-Affect-QuickBooks
- https://support.ollieorder.com/hc/en-us/articles/11415498736916-How-to-Import-Work-In-Progress-Inventory-WIP- (through the API)
- https://support.ollieorder.com/hc/en-us/articles/10881922084244-How-to-Add-Delete-Vendors-Quickbooks-Online-Integration-Sync
- https://support.ollieorder.com/hc/en-us/articles/10225756962452-QBO-Account-Override-Packaging-Materials
- https://support.ollieorder.com/hc/en-us/articles/10225600882068-QBO-Account-Override-Raw-Materials-Ingredients
- https://support.ollieorder.com/hc/en-us/articles/6821281897236-Packaging-Materials
- https://support.ollieorder.com/hc/en-us/articles/6351651116308-Getting-Started-Overview
- https://support.ollieorder.com/hc/en-us/articles/38486560344340-6-16-25-Finished-Goods-in-a-Batch-Multi-Product-Repackaging
- https://support.ollieorder.com/hc/en-us/articles/29838270209684-How-to-Generate-a-Billing-Statement
- https://support.ollieorder.com/hc/en-us/articles/29754653152148-Raw-Material-Forecast-by-Batch-Report
- https://support.ollieorder.com/hc/en-us/articles/18730505987988-Ollie-Ops-Single-Sign-Up
- https://support.ollieorder.com/hc/en-us/articles/18010498316180-Raw-Packaging-Material-Inventory-Management (through the API; video only)
- https://support.ollieorder.com/hc/en-us/articles/15204058119444-How-to-Record-a-Packaging-Run
- https://support.ollieorder.com/hc/en-us/articles/11415607311124-How-to-Import-Existing-Finished-Goods-Inventory
- https://support.ollieorder.com/hc/en-us/articles/6325268671124-Recipes
- https://support.ollieorder.com/hc/en-us/articles/29838550025620-Ollie-Security
- https://support.ollieorder.com/hc/en-us/articles/37712367941652-5-21-25-Task-Enhancements-and-Teams
- https://support.ollieorder.com/hc/en-us/articles/30936689312660-Projecting-Finished-Goods-Costs-via-Recipes
- https://support.ollieorder.com/hc/en-us/articles/29444744999316-Finished-Goods-Inventory-Management (through the API; video only)
- https://support.ollieorder.com/hc/en-us/articles/18731170810772-Enabling-Google-Sign-in-for-Ollie-Ops
- https://support.ollieorder.com/hc/en-us/articles/17123504679316-Raw-Material-Forecast-Report
- https://support.ollieorder.com/hc/en-us/articles/14167939061268-How-to-Create-a-Brewhouse-and-Applying-it-to-a-Recipe
- https://support.ollieorder.com/hc/en-us/articles/6373249895060-Packaging-Transactions
- https://support.ollieorder.com/hc/en-us/articles/37231904302612-4-23-25-Add-Raw-Materials-Additional-Costs-to-Packaging-Runs
- https://support.ollieorder.com/hc/en-us/articles/29838453415316-How-Do-I-Update-A-Payment-Method-On-A-Subscription
- https://support.ollieorder.com/hc/en-us/articles/29755578401556-Packaging-Material-Forecast-Report
- https://support.ollieorder.com/hc/en-us/articles/29445585361300-Adjustments (through the API; video only)
- https://support.ollieorder.com/hc/en-us/articles/8085461788692-How-To-Create-Products-Variants
- https://support.ollieorder.com/hc/en-us/articles/6324385354004-Brew-Day-Reviewing-Batches (video only)
- https://support.ollieorder.com/hc/en-us/articles/6318813014932-Transfers-Splitting-Batches
- https://support.ollieorder.com/hc/en-us/articles/37232424624404-4-7-25-Unit-of-Measure-Improvements
- https://support.ollieorder.com/hc/en-us/articles/34525762841876-Adding-Additions-To-A-Batch (video only)
- https://support.ollieorder.com/hc/en-us/articles/32957684751380-How-To-Create-a-Product-Map-It-Between-Ollie-Ops-Order (video only)
- https://support.ollieorder.com/hc/en-us/articles/29838617794068-How-Do-I-Find-My-Subscription-Next-Bill-Date
- https://support.ollieorder.com/hc/en-us/articles/29837294969236-Repackaging (video only)
- https://support.ollieorder.com/hc/en-us/articles/29446036311700-Sales (video only)
- https://support.ollieorder.com/hc/en-us/articles/19165278549652-Inviting-New-Users
- https://support.ollieorder.com/hc/en-us/articles/16976926313492-Batch-History-Report
- https://support.ollieorder.com/hc/en-us/articles/38784071882516-Inventory-Transfers-Between-Ops-Accounts (video only)
- https://support.ollieorder.com/hc/en-us/articles/37231997834900-4-2-25-New-Tax-Reports
- https://support.ollieorder.com/hc/en-us/articles/29919308711316-Reversing-Deleting-Transfers-Packaging-Runs-Repackaging
- https://support.ollieorder.com/hc/en-us/articles/29838681430548-How-Do-I-Add-A-New-Payment-Method
- https://support.ollieorder.com/hc/en-us/articles/29468993296276-Barrel-Actions-Management (video only)
- https://support.ollieorder.com/hc/en-us/articles/16977089366548-Production-Report
- https://support.ollieorder.com/hc/en-us/articles/10996924343700-How-to-Add-Raw-Material-Inventory-Edit-Ingredients
- https://support.ollieorder.com/hc/en-us/articles/6374040324756-System-Setup
- https://support.ollieorder.com/hc/en-us/articles/29838738693780-How-Do-I-Update-My-Billing-Contact
- https://support.ollieorder.com/hc/en-us/articles/29837454966420-How-To-Transfer-Inventory-from-Ollie-Ops-to-Ollie-Order (through the API; video only)
- https://support.ollieorder.com/hc/en-us/articles/17532426648980-Packaging-Reports-Packaged-Scheduled-Repackaging
- https://support.ollieorder.com/hc/en-us/articles/11010569372820-How-to-Add-Packaging-Materials-Starting-Inventory
- https://support.ollieorder.com/hc/en-us/articles/10878898411668-Brewery-Settings
- https://support.ollieorder.com/hc/en-us/articles/6821342451092-Yeast
- https://support.ollieorder.com/hc/en-us/articles/29838768653716-How-to-View-an-Invoice
- https://support.ollieorder.com/hc/en-us/articles/29447127042196-How-To-Add-A-Unit-Price-To-A-Raw-or-Packaging-Material (through the API; video only)
- https://support.ollieorder.com/hc/en-us/articles/16976970318356-Inventory-on-Hand-Report
- https://support.ollieorder.com/hc/en-us/articles/6372508453524-Quickbooks-Online-Integration (video only)
- https://support.ollieorder.com/hc/en-us/articles/6370888983444-Fermentation-Logs
- https://support.ollieorder.com/hc/en-us/articles/16977010744468-Inventory-Value-Report
- https://support.ollieorder.com/hc/en-us/articles/10881619274004-How-to-Manually-Add-Vendors
- https://support.ollieorder.com/hc/en-us/articles/6821241454740-Ingredient-Setup
- https://support.ollieorder.com/hc/en-us/articles/6371150189204-Updating-A-Batch-Volume
- https://support.ollieorder.com/hc/en-us/articles/38774951069076-Transfers-Report-for-multi-Ops-facilities (video only)
- https://support.ollieorder.com/hc/en-us/articles/31643268770964-How-To-Add-Multiple-Emails-To-A-Vendor-For-Purchase-Orders (through the API; video only)
- https://support.ollieorder.com/hc/en-us/articles/6374251139604-Packaging-Material-Setup
- https://support.ollieorder.com/hc/en-us/articles/36169232563220-Ops-x-Order-Data
- https://support.ollieorder.com/hc/en-us/articles/7052172574484-Package-Types-Setup
- https://support.ollieorder.com/hc/en-us/articles/6373090203028-Product-Setup
- https://support.ollieorder.com/hc/en-us/articles/6319905755540-Tax-Determination-Settings-TTB-Reporting
- https://support.ollieorder.com/hc/en-us/articles/16977138733332-Sales-Report
- https://support.ollieorder.com/hc/en-us/articles/6374612872724-Vendor-Setup
- https://support.ollieorder.com/hc/en-us/articles/29657668439700-Customers-Setup
- https://support.ollieorder.com/hc/en-us/articles/10227334080404-Transitioning-to-Ollie-Ops-from-Ekos
- https://support.ollieorder.com/hc/en-us/articles/14168292307476-Notification-Thresholds
- https://support.ollieorder.com/hc/en-us/articles/6371013087636-Custom-Fields
- https://support.ollieorder.com/hc/en-us/articles/6351238723092-Dashboard
- https://support.ollieorder.com/hc/en-us/articles/6323838281876-Creating-Managing-Barrels
- https://support.ollieorder.com/hc/en-us/articles/6319327660692-Purchase-Orders
- https://support.ollieorder.com/hc/en-us/articles/6319880329748-Tasks
- https://support.ollieorder.com/hc/en-us/articles/29839186459028-Ollie-Ops-Order-Overview (through the API; video only)

**Related articles found by search (outside the Ops category, or duplicates)**
- https://support.ollieorder.com/hc/en-us/articles/11646858595476-How-to-Set-Up-Ollie-Ops-Ollie-Order-Integration
- https://support.ollieorder.com/hc/en-us/articles/37231834034196-4-23-25-Move-Finished-Goods-from-Order-Back-to-Ops
- https://support.ollieorder.com/hc/en-us/articles/15117761217940-Enabling-Lot-Code-Tracking
- https://support.ollieorder.com/hc/en-us/articles/36390445592596-Ollie-and-QuickBooks-Online-Deep-Dive (Order-side copy, through the API)
- https://support.ollieorder.com/hc/en-us/articles/29837687141268-How-To-Transfer-Inventory-from-Ollie-Ops-to-Ollie-Order (Order-side copy; video only)
- https://support.ollieorder.com/hc/en-us/articles/30430578182164-Inventory-Adjustments-Inventory-Reconciliation-Reports (Order)
- https://support.ollieorder.com/hc/en-us/articles/11644531308820--Dashboard-Tank-Recipes-and-Vendors (onboarding video)
- https://support.ollieorder.com/hc/en-us/articles/11644571495188-Packaging-Types-Products-Product-Variants (onboarding video)
- https://support.ollieorder.com/hc/en-us/articles/11644535558548-Importing-Raw-Materials-and-Packaging-Materials (onboarding video)
- https://support.ollieorder.com/hc/en-us/articles/53488973181204-Part-VI-Tags-and-Section-5010-on-Materials (distilling; draft)
- https://support.ollieorder.com/hc/en-us/sections/8181509905812 (QuickBooks section index)
- https://support.ollieorder.com/hc/en-us/sections/30283001884308-The-Basics (Order basics index)

**Seen but not fetched or not readable:** 11644557042964 Purchase Orders (onboarding video; fetch failed), 53487774913300 Distilling Overview (fetch failed), and the other Distilling/DSP articles (out of scope). The Loom videos had no transcripts available.
