# Financial-Analysis-of-RAW-Data-by-Using-the-PowerBI
Financial Analysis Project

Overview
This Power BI project provides an end-to-end financial analysis and dashboard for [Project / Company name]. It ingests raw transaction and ledger data, performs ETL and business logic (DAX), and presents interactive visualizations for stakeholders to track revenue, profitability, cash & liquidity and business health across segments and time.

Files in this repo
- PowerBI/Financial Analysis Project.pbix — Power BI Desktop file (dashboard and model).
- data/Raw_* — raw CSV exports (transactions, customers, chart of accounts, etc.).
- data/Calculated_* — exported calculation tables used for the visuals (optional).
- analysis/sheets_report.csv — metadata about exported sheets.
- README.md — this file.
- screenshots/ — screenshots of main dashboards for quick view.

Data sources
- Transactions — transactional sales/invoice table (columns: InvoiceID, Date, CustomerID, ProductID, Quantity, UnitPrice, Discount, RevenueNet, Cost, GLAccount).
- GeneralLedger — ledger-level entries (Date, Account, Amount, Debit/Credit, Period, Segment).
- Customers — customer dimension (CustomerID, Segment, Region, AcquisitionDate).
- Products — product dimension (ProductID, Category, Cost, ListPrice).
- CurrencyRates — FX rates for converting to reporting currency (Date, Currency, Rate).
  
Data model (star schema)
- Fact tables: Transactions, GLEntries
- Dimension tables: Date, Customers, Products, Accounts, Regions
- Relationships: Transactions[Date] → Date[Date], Transactions[CustomerID] → Customers[CustomerID], Transactions[ProductID] → Products[ProductID], GLEntries[Account] → Accounts[Account]
Tip: Keep the date dimension marked as Date table in Power BI (Model → Date table) to enable time intelligence.

ETL & data cleaning steps (process)
- Import raw CSVs / connect to source.
- Normalize dates and currency (ensure Date column type).
- Create RevenueNet = (Quantity × UnitPrice) − Discount.
- Map product and account codes to readable names.
- Remove or flag test / refunded transactions.
- Fill missing dimensions or mark as Unknown.
- Aggregate GL entries monthly for balance sheet metrics.
- Create period columns (Year, Month, FY, FYMonthNo) for reporting.

3. Key financial terms (explained — great for README/LinkedIn)
- Revenue (Net Sales) — Total income from sales after discounts and returns.
- Formula example: SUM(Transactions[RevenueNet]).
- COGS (Cost of Goods Sold) — Direct costs of producing goods sold (material + direct labor).
- Used to compute gross profit.
- Gross Profit — Revenue − COGS. Measures direct profitability of sales.
- Gross Margin (%) — Gross Profit / Revenue. Indicates how much of revenue remains after direct costs.
- Operating Expenses (OPEX) — Indirect costs (SG&A, marketing, R&D). Excluded from COGS.
- EBITDA — Earnings before interest, taxes, depreciation & amortization. Useful operating performance metric: Revenue − COGS − OPEX + Other Operating Income.
- Operating Margin (%) — EBITDA / Revenue (or Operating Income / Revenue depending on definition).
- Net Profit (Net Income) — Bottom-line profit after interest, taxes, depreciation.
- YoY Growth (Year-over-Year) — (Current Period − Same Period Last Year) / Same Period Last Year.
- MoM Growth (Month-over-Month) — (Current Month − Previous Month) / Previous Month.
- CAGR (Compound Annual Growth Rate) — ((End / Start)^(1/years)) − 1
- ARPU (Average Revenue Per User) — Revenue / Number of Active Customers.
- Churn Rate — % customers lost over a period.
- LTV (Customer Lifetime Value) — Avg revenue per user × avg gross margin × avg customer lifetime.
- DSO (Days Sales Outstanding) — Accounts Receivable / Average Daily Sales — measures how long customers take to pay.
- Liquidity Ratios — e.g., Current Ratio = CurrentAssets / CurrentLiabilities. Quick Ratio = (CurrentAssets − Inventory) / CurrentLiabilities.
- Debt-to-Equity Ratio — Total Debt / Total Equity.

4. DAX measures (paste into Power BI)
Use these examples as a starting point. Replace table/column names with your model names.
-- Basic sums
Total Revenue = SUM(Transactions[RevenueNet])
Total COGS = SUM(Transactions[Cost])
-- Profit & margin
Gross Profit = [Total Revenue] - [Total COGS]
Gross Margin % = DIVIDE([Gross Profit], [Total Revenue], 0)
-- Operating metrics (assuming Opex is in GLEntries segregated)
Total Opex = CALCULATE(SUM(GLEntries[Amount]), FILTER(Accounts, Accounts[Type]="OPEX"))
EBITDA = [Gross Profit] - [Total Opex]
EBITDA Margin % = DIVIDE([EBITDA], [Total Revenue], 0)
-- Time intelligence
Revenue LY = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Date[Date]))
YoY Revenue % = DIVIDE([Total Revenue] - [Revenue LY], [Revenue LY], 0)
-- Month over month
Revenue Prev Month = CALCULATE([Total Revenue], PREVIOUSMONTH(Date[Date]))
MoM Growth % = DIVIDE([Total Revenue] - [Revenue Prev Month], [Revenue Prev Month], 0)
-- ARPU
Active Customers = DISTINCTCOUNT(Transactions[CustomerID])
ARPU = DIVIDE([Total Revenue], [Active Customers], 0)
-- DSO (example - requires AR and Sales values)
Average Daily Sales = DIVIDE([Total Revenue], DISTINCTCOUNT(Date[Date])) -- refine as needed
DSO = DIVIDE([AccountsReceivableTotal], [Average Daily Sales], 0)
5. Visuals (what to build + interpretation guidance)
A. Executive summary / KPI card row
KPIs: Total Revenue, Gross Profit, EBITDA, Net Profit, Gross Margin %, EBITDA Margin %, MoM Growth, YoY Growth, DSO.
Interpretation: Use conditional formatting (green/red) and small deltas showing YoY / MoM.
B. Revenue Trend (time series)
Visual: Line chart showing Revenue by Month/Quarter.
Insights to capture: seasonality, trend slope, significant dips/rises and corresponding causes (e.g., promotions, product launch).
C. Waterfall (Revenue to Net Income)
Visual: Waterfall from Revenue → COGS → Opex → EBITDA → Depreciation/Taxes → Net Income.
Interpretation: Where most erosion occurs — focus on cost reduction if COGS is dominant.
D. Revenue by Segment (Stacked bar / 100% stacked)
Visual: Revenue by Product Category or Region.
Insights: Top contributors and dependency risk (Pareto: top 20% customers giving 80% revenue).
E. Margin heatmap by Product vs Region
Visual: Matrix or heatmap.
Insights: Products/regions with high revenue but low margins vs low revenue high margin.
F. Cohort / Retention chart (if customer data exists)
Visual: Cohort retention table or line chart.
Insights: Customer retention over months, acquisition channel performance.
G. AR & DSO trend
Visual: Line chart of AR balance and DSO over time.
Insights: payment delays, need for collection focus.
H. Profitability by Customer (Pareto)
Visual: Pareto chart (sorted bar + cumulative line).
Insights: Top customers, concentration risk.
For each visualization in your README include:
what it shows,
how to interpret it,
recommended action if metric deviates from target.

6. Example insights report (template — replace placeholders with real nums)
Executive summary
Total Revenue (FY2024): ₹ [Total Revenue] — YoY growth [YoY%] (vs FY2023).
Gross Profit: ₹ [GrossProfit] (Gross Margin [GrossMargin%]).
EBITDA: ₹ [EBITDA] (EBITDA Margin [EBITDAMargin%]).
Net Profit: ₹ [NetProfit] (Net Margin [NetMargin%]).
DSO: [DSO] days — trend: increasing/decreasing by [Δ days] vs prior period.
Top-line drivers
Top 3 products by revenue: ProductA: X%, ProductB: Y%, ProductC: Z% of total revenue.
Top region: RegionName contributing [RegionShare%] of total revenue.
Top 10 customers contribute [Top10Share%] of revenue — concentration risk: High/Medium/Low.
Margin & cost analysis
COGS as % of Revenue: [COGS%] — key drivers: raw material inflation, vendor costs.
Operating expenses breakdown: Marketing [M%], G&A [G%], R&D [R%] of revenue.
Largest margin erosion item: e.g., Increased Freight (impact: ₹[amount]).
Cash & liquidity
Cash balance trend: up/down by [Δ%].
Current Ratio: [CurrentRatio] — acceptable range: 1.2–2.0.
Alert: AR aging >90 days = ₹ [AR_90plus] — recommend collection drive.
Recommendations (actionable)
- Reduce COGS by renegotiating supplier contracts — target a 200 bps improvement to gross margin.
- Focus retention on the top 10 customers to mitigate concentration risk — introduce loyalty discounts.
- Improve collections: target DSO reduction of [targetDays] to free up ₹[cashImpact] in working capital.
- Reassess unprofitable product-region combinations — consider price changes or discontinuation.
computes Total Revenue, Gross Profit, YoY%, Top N customers,

exports a summary CSV.
