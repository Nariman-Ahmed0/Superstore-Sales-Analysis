# Superstore Sales Analysis | Power BI

An interactive Power BI report built on a **star schema** over 9,800 US Superstore order lines (January 2015 to December 2018). The project covers data modeling in Power Query, DAX measures, a Sales Overview page with 6 charts, and written business insights.

![Sales Overview page](screenshots/sales_overview.png)

---

## Table of Contents

1. [Project Goal](#project-goal)
2. [Dataset](#dataset)
3. [Data Model](#data-model)
4. [DAX Measures](#dax-measures)
5. [Report Pages](#report-pages)
6. [Key Insights](#key-insights)
7. [Data Limitations](#data-limitations)
8. [Tools and Skills](#tools-and-skills)

---

## Project Goal

Turn a single flat CSV file into a clean star schema and build a one-page sales report that answers:

- How do sales change from year to year?
- Which months are the strongest?
- Which category, sub-category, region and state sell the most?
- What do the numbers mean for the business, and what should be investigated next?

---

## Dataset

The original CSV is in the `data` folder.

| Item | Detail |
|---|---|
| Rows | 9,800 order lines |
| Columns | 18 |
| Period | 3 January 2015 to 30 December 2018 |
| Geography | United States (4 regions) |
| Only numeric measure | `Sales` |

---

## Data Model

A star schema with one fact table and five dimension tables, connected with one-to-many relationships.

![Model view](screenshots/model_view.png)

| Table | Type | Contents |
|---|---|---|
| `Fact_Sales` | Fact | Order ID, Order Date, Ship Date, Customer ID, Product ID, Location ID, ShipMode ID, Sales |
| `Dim_Customer` | Dimension | Customer ID, Customer Name, Segment |
| `Dim_Product` | Dimension | Product ID, Product Name, Category, Sub-Category |
| `Dim_Location` | Dimension | Location ID, City, State, Postal Code, Region |
| `Dim_ShipMode` | Dimension | ShipMode ID, Ship Mode |
| `Dim_Calendar` | Dimension | Date, Year, Month Number, Month Name, Quarter, Day Name |
| `_Measures` | Measures table | All DAX measures |

`Dim_Calendar` is built with DAX (`CALENDARAUTO`), marked as the date table, and `Month Name` is sorted by `Month Number`.

**Data preparation (Power Query):** the dimensions and the fact table were created as references of one staging query, surrogate keys were added to the dimensions and merged back into the fact table, and dates were parsed with the `dd/mm/yyyy` locale.

**Data quality issues found:** 11 blank Postal Codes (all Burlington, Vermont), 32 Product IDs that map to more than one product name, and 1 duplicate order line when ignoring Row ID.

---

## DAX Measures

| Measure | Result | DAX |
|---|---|---|
| Total Sales | $2,261,536.78 | `SUM('Fact_Sales'[Sales])` |
| Total Orders | 4,922 | `DISTINCTCOUNT('Fact_Sales'[Order ID])` |
| Total Customers | 793 | `DISTINCTCOUNT('Fact_Sales'[Customer ID])` |
| Sold Products | 1,861 | `DISTINCTCOUNT('Fact_Sales'[Product ID])` |
| Order Lines | 9,800 | `COUNTROWS('Fact_Sales')` |
| Avg Sales Per Customer | $2,851.87 | `DIVIDE([Total Sales],[Total Customers])` |
| Avg Order Value | $459.48 | `DIVIDE([Total Sales],[Total Orders])` |

---

## Report Pages

### Sales Overview Page

5 KPI cards, 2 slicers (Year and Region) and 6 charts:

| Chart | Question it answers |
|---|---|
| Column: Total Sales by Year | How do sales change from year to year? |
| Line: Total Sales by Month, one line per Year | When are sales strongest? |
| Donut: Total Sales by Category | Which category sells the most? |
| Bar: Total Sales by Sub-Category (Top 10) | Which sub-categories lead? |
| Column: Total Sales by Region | Which region is the strongest? |
| Bar: Total Sales by State (Top 10) | Where are sales concentrated? |

### Key Insights Page

![Key Insights page](screenshots/key_insights.png)

### Data Limitations Page

![Data Limitations page](screenshots/data_limitations.png)

---

## Key Insights

### 1. Sales grew strongly after a dip in 2016
- **Finding:** 2018 is the best year at $722K, up 20.3% on 2017 ($600K). 2017 grew 30.6% over 2016 ($459K), which was the only down year (-4.3% vs 2015's $480K).
- **Why it matters:** Two years of 20%+ growth suggest real expansion rather than a recovery.
- **Next step:** Check whether growth came from new customers or from existing ones buying more.

### 2. Sales are back-loaded: September, November and December carry 43% of the year
- **Finding:** November is the peak (15.5% of all sales), then December (14.2%) and September (13.3%). Q4 alone is 38.5%. January and February are the weakest, together only 6.8%. The same shape repeats in all four years.
- **Why it matters:** Stock, staffing and promotions should be planned around a predictable autumn peak and a quiet start to the year.
- **Next step:** Find what drives September and test off-season promotions for January and February.

### 3. Technology leads, but the three categories are close
- **Finding:** Technology is 36.6% of sales ($827K), Furniture 32.2% ($729K) and Office Supplies 31.2% ($705K). The gap between first and last is only 5.4 percentage points.
- **Why it matters:** Revenue is well balanced, so there is no single-category dependency risk.
- **Next step:** Add Profit data to see which category earns the most.

### 4. Two sub-categories drive 29% of sales, and the tail is tiny
- **Finding:** Phones ($328K) and Chairs ($323K) together make up 28.8% of sales. At the bottom, Fasteners sell only $3K (0.1%).
- **Why it matters:** A few products carry the business while several sub-categories add almost nothing.
- **Next step:** Review the weakest sub-categories (keep, bundle or drop) and protect stock for Phones and Chairs.

### 5. West leads and South lags by almost half
- **Finding:** West sells $710K (31.4%) and South $389K (17.2%), so West sells 1.8x as much. West also has far more orders (1,587 vs 810 in the South), so the gap comes from order volume.
- **Why it matters:** The South has fewer orders from a similar customer base (509 customers vs 681 in West), which points to an engagement or reach problem.
- **Next step:** Check shipping, assortment and marketing coverage in the South.

### 6. Sales are concentrated in a handful of states
- **Finding:** The top 10 states make up 69.1% of sales ($1.56M of $2.26M). California ($446K) and New York ($306K) alone are 33.3%, out of 49 states in the data.
- **Why it matters:** The business depends heavily on two states.
- **Next step:** Study why the other ~39 states contribute only 31% and where expansion is easiest.

---

## Data Limitations

The dataset contains only **Sales** values, which limits the questions this report can answer.

**What it cannot answer**
- **Profitability:** which category, sub-category, region or product is the most profitable.
- **Discounts:** whether discounts increase sales or reduce profit.
- **Volume vs. price:** whether a product sells more because customers buy more units or because its price is higher.

**What would be needed:** Profit (or Cost), Discount and Quantity columns.

**What it can still explain:** Total Orders and Total Customers are in the report. For example, West has 1,587 orders vs 810 in the South, which helps explain the regional sales gap.

---

## Tools and Skills

- **Power BI Desktop**
- **Power Query (M):** data cleaning, reference queries, merges, surrogate keys
- **DAX:** calendar table and business measures
- **Data modeling:** star schema, relationships, date table, sort-by-column
- **Data visualization and storytelling:** KPI cards, slicers, Top N filters, insight writing

