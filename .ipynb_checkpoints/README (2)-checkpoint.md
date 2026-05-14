# Inventory Intelligence System - The Lip Tribe

**Tools:** Excel · Power BI · DAX &nbsp;|&nbsp;  **Domain:** Retail Analytics · Inventory Management 


## Project Background

The Lip Tribe is a Kenyan beauty retail brand selling premium lip care products like Balms, Oils and Glosses across 5 brands namely Summer Fridays, Fenty Beauty, Rhode, Rare Beauty and Glow Lab. The business operates from a single CBD branch in Nairobi and manages 43 active SKUs.

This project was inspired by a [Founder Diaries Podcast](https://youtu.be/cxG8EpFUOXQ?si=Wwjwv0sI7rKNY6J5) interview featuring Kenyan entrepreneur Murugi Munyi (The Lip Tribe), where she discussed the real operational challenges facing her business like cash tied up in slow moving stock, no real-time visibility into what is selling and restocking decisions made on gut feeling rather than data.

I am a data analyst approaching this from the outside I observed the business model, identified the core inventory pain points, built a simulated dataset reflecting real retail dynamics and created a fully automated Power BI dashboard that the business owner can access in real time from their phone.

The analysis covers January to December 2025** across 4 key areas:

- Revenue & Profitability Performance
- Inventory Health & Stock Classification
- Sales Velocity & Run Rate Analysis
- Restock Intelligence & Demand Forecasting


## Data Structure & Initial Checks

The data model follows a star schema consisting of 4 tables with a total of 10,836 records:

| Table | Rows | Description |
|---|---|---|
| **Products** | 43 | Master product list — SKU, Brand, Category, Cost Price, Selling Price, Reorder Level, Lead Time |
| **Sales** | 8,796 | Transaction-level sales records — Date, Invoice, SKU, Quantity Sold, Selling Price |
| **Inventory_Purchases** | 146 | Restocking history — Date, SKU, Quantity Purchased, Unit Cost |
| **Calendar** | 365 | Date dimension — Date, Year, Month, Quarter, Week |

**Relationships (Star Schema):**

```
Calendar ─(1:M)──► Sales
Calendar ─(1:M)──► Inventory_Purchases
Products ─(1:M)──► Sales
Products ─(1:M)──► Inventory_Purchases
```

**Data cleaning steps performed:**
- Removed broken `Launch_date` TEXT formula column from Products
- Removed pre-calculated `Revenue` and `Margin_Pct` columns — recalculated in DAX
- Rebuilt `Inventory_Purchases` table with realistic restock batches spread across the year (original dataset had only one purchase batch per SKU causing negative stock across all 43 products)
- Removed sales rows for 5 No Sales SKUs to correctly trigger `Run Rate = 9999` classification in DAX
- Fixed all data types — dates as Date, quantities as Whole Number, prices as Integer
- Verified zero nulls, zero duplicate SKUs, zero duplicate invoices, zero negative quantities


## Executive Summary

### Overview of Findings

The Lip Tribe generated Ksh 51M in revenue for 2025 with a healthy 62% gross margin but Ksh 435K remains locked in slow moving and dead stock cash that could be reinvested into faster moving products. The business has a clear imbalance: 29 SKUs are Fast movers driving the majority of revenue while 14 SKUs (Slow + No Sales) are silently eroding cashflow. The restock intelligence layer reveals that 10 SKUs are at critical stockout risk with fewer than 6 days of stock remaining meaning the business is simultaneously over-stocked on products nobody wants and running out of products that customers are actively buying.

![Overview Dashboard](screenshots/overview.png)



## Insights Deep Dive

### Revenue & Profitability

**Total Revenue: Ksh 51M &nbsp;|&nbsp; Gross Profit: Ksh 31M &nbsp;|&nbsp; Gross Margin: 62%**

- Revenue peaked in November–December reaching Ksh 5M - 6M monthly a clear seasonal pattern driven by festive gifting. January - September revenue stabilised between Ksh 2.5M - 4M per month suggesting the business is highly dependent on Q4 performance.

- Gross margin held consistently at 62% across all 12 months indicating stable pricing discipline and no significant cost pressure. This is a strong foundation for scaling.

- Summer Fridays and Rare Beauty each contributed Ksh 13M in revenue. The two dominant brands accounting for over 50% of total revenue. Rhode contributed only Ksh 4M significantly underperforming relative to its premium positioning suggesting either pricing, placement or awareness issues at the store level.

- Revenue by category; Balm leads at Ksh 23M (45%)**, Oil at Ksh 14M (27%), Gloss at Ksh 14M (27%). The Balm dominance aligns with lip care market trends where treatment focused products outperform cosmetic only products.

![Cash Flow Dashboard](screenshots/cashflow.png)



### Inventory Health & Stock Classification

**Inventory Value: Ksh 2M &nbsp;|&nbsp; Capital in Slow Stock: Ksh 435K &nbsp;|&nbsp; Fast Movers: 29 SKUs**

- 29 of 43 SKUs (67%) are classified as Fast movers with Run Rate Days ≤ 20. This is a healthy ratio indicating the product mix is broadly well matched to customer demand.

- Ksh 435K is tied up in Slow and No Sales SKUs and this represents 21% of total inventory value sitting on shelves generating zero return. For a small retail business this is significant working capital that could fund restocking of fast movers or investment in new products.

- 5 SKUs (LT-009, LT-017, LT-025, LT-030, LT-037) recorded zero sales across the full year. These products are consuming shelf space and capital with no commercial return a clear signal for ranging review or promotional clearance.

- The inventory aging chart reveals LT-040 has the highest run rate days over 40 days of stock remaining while simultaneously being the top revenue SKU. This apparent contradiction resolves when we see LT-040 has high stock from aggressive purchasing not slow sales.

![Inventory Dashboard](screenshots/inventory.png)



### Sales Velocity & Run Rate

**Avg Daily Sales: 44.24 units &nbsp;|&nbsp; Total Transactions: 9K &nbsp;|&nbsp; Revenue Last 30 Days: Ksh 7M**

- Daily unit sales surged from 1,100 in January to a peak of 2,100 in December before dropping sharply in February confirming the strong seasonality pattern seen in revenue. The February dip to 1,000 units is the sharpest single month decline suggesting post festive demand correction.

- LT-018, LT-032 and LT-001 are the fastest moving SKUs with run rates of 3 – 4 days meaning they sell through their current stock in under a week. These are the crown jewels of the product range and the highest stockout risk simultaneously.

- Revenue by brand shows Summer Fridays and Rare Beauty leading at Ksh 13M each. The relative performance of Glow Lab at Ksh 9M is notable a newer brand closing in on the established players suggesting growing customer acceptance.

- Avg Run Rate across all active SKUs is 1.18K days this high average is distorted by the 5 No Sales SKUs pulling the average up. Excluding No Sales SKUs the true average run rate is approximately 18 days firmly in the Fast category for the active product range.

![Sales Dashboard](screenshots/sales.png)



### Restock Intelligence

**Restock Alerts: 10 SKUs &nbsp;|&nbsp; Avg Days to Stockout: 14.5 days &nbsp;|&nbsp; Suggested Reorder Value: Ksh 910K**

- 10 SKUs have triggered the Restock Alert meaning current stock is at or below the reorder level AND the product has active daily sales. All 10 are at critical risk with fewer than 6 days of stock remaining.

- LT-026 requires the largest reorder of 92 units driven by its exceptionally high daily sales velocity of 4.52 units per day combined with only 22 units in stock. A stockout on LT-026 would be the most commercially damaging given it is a top revenue contributor.

- The +20% safety buffer built into the Suggested Reorder Qty formula accounts for supplier lead time variability and unexpected demand spikes ensuring the business never cuts reorder quantities too fine. At 14 - day average lead time for Kenyan beauty suppliers this buffer represents approximately 2–3 days of extra cover.

- Ksh 910K is needed to fully restock all alerted SKUs a concrete, actionable number that allows the CEO to make an immediate cash allocation decision rather than manually calculating per product order quantities.

![Restock Dashboard](screenshots/restock.png)



## Recommendations

Based on the analysis above, I recommend the following actions for The Lip Tribe:

1. Immediately restock the 10 alerted SKUs; LT-001, LT-008, LT-018, LT-022 and LT-032 have fewer than 4 days of stock remaining. A stockout on any of these means lost sales on the fastest moving products. Budget Ksh 910K for immediate restock.

2. Initiate a clearance strategy for the 5 No Sales SKUs. Bundle them as gift sets, run a promotional discount or remove them from the range entirely. Recovering even 60% of the Ksh 435K locked in slow stock frees up Ksh 260K for reinvestment in fast movers.

3. Investigate Rhode's underperformance at Ksh 4M against Summer Fridays' Ksh 13M, Rhode is generating 3x less revenue despite comparable product positioning. Root cause analysis should examine placement, pricing, staff recommendation frequency and customer awareness.

4. Prepare for Q4 demand surge. The December peak of Ksh 5M - 6M is 2x the monthly average. The business should begin building reorder buffers for fast moving SKUs by October to avoid stockouts during the highest revenue period of the year.

5. Review the Balm forward product mix as Balm accounts for 45% of revenue. While this strength should be protected, the Oil and Gloss categories at 27% each represent growth opportunities if the right SKUs are identified and promoted.



## Technical Implementation

**Data Model:**
- Star schema with 4 tables and 4 active relationships
- All calculations in DAX no pre-calculated columns in Excel
- 18 DAX measures across 4 categories: Sales, Inventory, Profitability, Intelligence

**Key DAX Measures:**

```dax
-- Inventory classification
Stock Classification =
SWITCH( TRUE(),
    [Run Rate Days] <= 20,   "Fast",
    [Run Rate Days] <= 45,   "Medium",
    [Run Rate Days] = 9999,  "No Sales",
    "Slow"
)

-- Restock recommendation with 20% safety buffer
Suggested Reorder Qty =
MAX( 0, CEILING(
    [Avg Daily Sales] * SELECTEDVALUE( Products[Lead_Time_Days], 14 ) * 1.2
    - [Current Stock], 1
))

-- Capital tied in dead stock
Capital in Slow Stock =
CALCULATE( [Inventory Value],
    FILTER( ALL( Products[SKU] ),
        [Stock Classification] = "Slow" || [Stock Classification] = "No Sales"
    )
)
```

**Dashboard Features:**
- 5-page interactive report: Overview · Inventory Health · Sales & Run Rate · Cash Flow · Restock Intelligence
- Global slicers: Brand · Category · SKU · Quarter · Branch
- Month tile slicer synced across all pages
- Conditional formatting on restock table (Red < 7d · Orange 7–14d · Green > 14d)
- Mobile layout optimised for CEO phone access
- Automated refresh via Power BI Service + OneDrive connection



## Files in This Repository

```
📁 Inventory-Intelligence-System/
├──  Inventory_Intelligence_System.pbix   ← Power BI dashboard
├── 📂 data/
│   ├──Lip_Beauty_Retail_PowerBI_Ready.xlsx  ← Raw dataset (4 sheets)
    ├──Lip_Beauty_Retail_PowerBI_Ready.xlsx  ← Clean dataset (4 sheets)
├── 📂 screenshots/
│   ├── overview.png
│   ├── inventory.png
│   ├── sales.png
│   ├── cashflow.png
│  └── restock.png
├──  Inventory-intelligence-walkthrough.mp4   ← Project walkthrough video
└──  README.md
```



## Assumptions & Caveats

- Simulated dataset - the data was generated based on observed retail dynamics from The Lip Tribe's public business information. It is not real proprietary data. Product names, SKU codes, prices and sales figures are simulated to reflect realistic Kenyan beauty retail patterns.

- Single branch - the analysis covers only the CBD Nairobi branch. Multi-branch dynamics, inter-branch stock transfers and branch level margin differences are not modelled.

- Inventory_Purchases rebuilt - the original dataset had only one purchase batch per SKU on January 1st, causing negative current stock across all 43 products. The table was rebuilt with realistic restock batches spread across 2025 to produce accurate stock classifications.

- 365-day average for Avg Daily Sales - daily sales velocity uses the full year as the denominator. In a live implementation this would use a rolling 30-day window to better reflect recent demand patterns.

- Static cost prices - Cost_Price is assumed constant throughout 2025. Real-world price fluctuations from suppliers, currency movements and bulk purchase discounts are not modelled.

- Lead time fixed at 14 days - the reorder calculation uses a default lead time of 14 days where no product specific lead time is specified. Actual supplier lead times in Nairobi may vary.


## Author

**Abishang Mueni**
Data Analyst | Power BI | Excel | DAX

[![LinkedIn](https://www.linkedin.com/in/abishang-mueni-6b1bb216a/)


*This project was built as part of a portfolio strategy to demonstrate real-world data analytics value to Kenyan SMEs, walking into businesses with working dashboards built on their own business model, showing stakeholders what data-driven decision-making looks like in practice.*
