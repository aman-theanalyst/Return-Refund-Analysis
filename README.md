# 🔄 Return & Refund Analysis Dashboard

> An end-to-end e-commerce analytics dashboard built using Power BI to uncover why orders are returned, where refund money is leaking, and which fixes should be prioritised first.

* * *

## 📌 Table of Contents

- [Overview](#-overview)
- [Business Problem](#-business-problem)
- [Dataset & Data Model](#-dataset--data-model)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Dashboard Views](#-dashboard-views)
- [Business Impact](#-business-impact)
- [Key Insights](#-key-insights)
- [How to Run This Project](#-how-to-run-this-project)
- [Final Recommendations](#-final-recommendations)
- [What Makes This Project Stand Out](#-what-makes-this-project-stand-out)
- [Author & Contact](#-author--contact)

* * *

## 🚀 Overview

The **Return & Refund Analysis Dashboard** is a business intelligence solution that gives e-commerce, operations and finance teams clear visibility into return behaviour and refund leakage across **3,100 orders (4,286 order lines) placed in 2025**.

It enables stakeholders to:

- Monitor core KPIs (Total Orders, Returned Orders, Return Rate %, Refund Amount) with month-over-month movement.
- Identify the "vital few" return reasons using Pareto analysis.
- Pinpoint exact root-cause clusters through a Subcategory × Return Reason heatmap.
- Compare return behaviour across categories and sales channels.
- Test whether discounting is linked to higher return risk.

This project simulates a real-world analytics engagement using Power Query transformation, a star-schema data model, advanced DAX measures and decision-focused visual design.

**Headline numbers**

| Total Orders | Returned Orders | Return Rate | Refund Amount | Avg Return Days | Products Returned |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **3,100** | **745** | **24.03%** | **₹3.82M** | **14.9 days** | **104** |

* * *

## 🎯 Business Problem

The business could see that returns were happening but could not explain them fast enough to act. Four problems stood out:

1. **Refund leakage:** money leaves through refunds, but the category, reason or channel behind it is unknown.
2. **Messy return reasons:** reasons arrive from multiple channels and need clean grouping before analysis.
3. **Hidden root causes:** category-level charts hide the real issue, which sits in subcategory and reason combinations.
4. **Discount risk:** heavy discounts may create low-intent purchases and raise return probability.

Key questions this dashboard answers:

- How large is the return problem, and is it getting better or worse month to month?
- Which return reasons, categories and channels create the most returned units and the most refund value?
- Which subcategory and reason combinations are the real hot spots?
- Which products carry the highest return risk compared with the overall benchmark?
- Is discounting actually driving returns?

> **Dashboard objective:** move from *"returns happened"* to *"why returns happened and what to fix first."*

* * *

## 📂 Dataset & Data Model

- **Period:** 1 Jan 2025 to 31 Dec 2025
- **Grain:** one row per product line within an order (4,286 lines, 3,100 distinct orders, 824 customers, 12 states)
- **Channels:** Website, Mobile App, Marketplace, Offline Store
- **Catalogue:** 6 categories, 13 subcategories, multiple brands
- **Payment methods:** UPI, Credit Card, Debit Card, Cash on Delivery, Wallet, Net Banking, Cash

The raw order-line table is modelled into a **Star Schema** for fast queries and accurate aggregations.

**Data Model Architecture:**

- **Fact Table:** `fact_orders` (Order IDs, Order/Delivery/Return dates, Quantity, Return Quantity, Unit Price, Unit Cost, Discount %, Shipping Fee, Refund Deduction %, Product Rating, Return Flag, Return Days)
- **Dimension Tables:**
  - `dim_products` (Product, Category, Subcategory, Brand)
  - `dim_customers` (Segment, City, State)
  - `dim_channel` (Sales Channel)
  - `dim_payment-methods` (Payment Method)
  - `dim_return-reason` (Return Reason)
  - `dim_date` (DAX-generated calendar built from the order date range)
- **Measure Tables:** `measure1` and `measure2` (KPI, chart and MoM DAX measures)

![Data model](https://github.com/aman-theanalyst/Return-Refund-Analysis/blob/main/dashboard_screenshots/Data%20Model.png)

**Key KPIs engineered using DAX:**

| KPI | Definition |
|---|---|
| **Total Orders** | Distinct count of Order IDs (orders, not order lines) |
| **Returned Orders** | Orders with at least one returned item |
| **Return Rate %** | Returned Orders ÷ Total Orders |
| **Refund Amount** | Returned Qty × Unit Price × (1 − Discount %) × (1 − Refund Deduction %) |
| **Returned Units** | Sum of returned quantity |
| **Avg Return Days** | Average days from delivery to return |
| **MoM Variances** | Previous-month comparison with ▲ ▼ arrows for each KPI card |

Other techniques: `RANKX` + cumulative share for the Pareto, `TOPN` + `CONCATENATEX` for dynamic "Top Category / Top Reason" cards, `UNICHAR` for trend arrows, and `REMOVEFILTERS` for the risk-colour benchmark (high risk is a return rate above 1.2× the overall rate).

* * *

## 🛠 Tech Stack

- **Power BI** (Data Modeling, Dashboard Design & Visualization)
- **DAX** (KPI Calculations, Time Intelligence, Pareto Ranking, Dynamic Measures, Conditional Formatting)
- **Power Query (M)** (Data Cleaning, Dimension Extraction, Surrogate Keys)
- **Python (pandas)** (supplementary insight analysis on the same data model)
- **Git & GitHub** (Version Control & Portfolio Hosting)

* * *

## 📂 Project Structure

```
📦 Return-Refund-Analysis
┣ 📷 Dashboard Images         (Power BI screenshots, optional)
┣ 📷 Images                   (charts and diagrams used in this README)
┣ 📄 dataset
┣ 📊 Return_Refund_Analysis_Dashboard.pbix
┣ 📑 Return_Refund_Analysis_Presentation.pptx
┗ 📄 README.md
```

* * *

## 🗂 Dashboard Views

The dashboard is organised into **two analytical layers**, moving from "what happened" to "why it happened".

### 1️⃣ Executive Summary

- Four KPI cards: Total Orders, Returned Orders, Return Rate %, Refund Amount, each with a month-over-month arrow.
- **Monthly trend:** refund amount and returned orders over time to detect spikes and seasonality.
- **Return Reason Pareto:** ranks reasons by returned units with a cumulative % line.
- **Returns by Category:** total orders versus returned units.
- **Returns by Sales Channel:** donut showing where returned units are concentrated.
- **Slicers:** Date Range, Category, Sales Channel, Reset Filters.

![Executive Summary](https://github.com/aman-theanalyst/Return-Refund-Analysis/blob/main/dashboard_screenshots/Executive%20Summary.png)

### 2️⃣ Product Diagnostic

- Cards: Avg Return Days (14.9), Products Returned (104), Top Category (Fashion), Top Reason (Product Not As Expected).
- **Subcategory × Return Reason Heatmap** with a dusty-red colour scale to expose exact root-cause clusters.
- **Discount vs Return Risk** scatter, with bubbles coloured by risk level against the overall benchmark.
- **Product Return Details** table: orders, returned units, return rate, refund amount, avg return days, avg discount.
- **Slicers:** Date Range, Category, Sales Channel, Return Reason, Reset Filters.
- Low-volume products are excluded from risk visuals to avoid false insights.

![Product Diagnostic](https://github.com/aman-theanalyst/Return-Refund-Analysis/blob/main/dashboard_screenshots/Product%20Diagnostics.png)

* * *

## 📈 Business Impact

By using this dashboard, the business can:

- **Stop refund leakage** by directing effort to the few reasons, products and channels that hold most of the refund value.
- **Reduce preventable returns** by separating fulfilment and quality failures from customer-preference returns.
- **Improve product listings and sizing** where "Product Not As Expected" and "Wrong Size" cluster.
- **Strengthen vendor and channel accountability** with evidence on damage, defects and wrong-item rates.
- **Plan for peak season** with early visibility of Q4 return spikes.

* * *

## 📊 Key Insights

**From the dashboard**

✅ **Roughly 1 in 4 Orders Comes Back**  
745 of 3,100 orders were returned (24.03%), and refunds of ₹3.82M equal about **17.9% of ₹21.36M sales after discount**.

✅ **Five Reasons Explain 79% of Returned Units**  
Product Not As Expected (228 units), Wrong Size (217) and Damaged Product (184) alone make up 56.5% of returned units. Adding Quality Issue and Wrong Item takes the total to 79.1%.

✅ **Fashion Drives Volume, Electronics Drives Money**  
Fashion has the highest return rate (26.1%) and most returned units (392). Electronics returns only 21.7% of orders but generates **₹1.56M, or 41% of all refund value** (average refund per return ₹7,769 vs ₹3,147 for Fashion).

✅ **Wrong Size Is a Concentrated Fashion Problem**  
Men's Clothing (80 units), Footwear (61) and Women's Clothing (51) × Wrong Size together equal **17% of all returned units**. Men's Clothing has the highest subcategory return rate at 29.8%.

✅ **Damaged Product Is the Costliest Reason**  
It is only third by units but **first by refund value (₹803K, 21%)**, because damage skews towards high-priced items such as Smart Devices, Kitchen and Audio.

✅ **Marketplace and Mobile App Return the Most**  
Marketplace (26.7%) and Mobile App (25.8%) lead on order-level return rate, and Mobile App alone accounts for ₹1.33M (35%) of refund value.

**Additional analysis beyond the dashboard**

✅ **Ratings Are the Strongest Early-Warning Signal**  
1-star lines return at **82%** and 2-star lines at 72%, versus just **3% for 5-star** lines. Returned items average 2.3 stars against 4.2 for kept items. (Only 49% of lines carry a rating, so coverage itself is a gap.)

✅ **Discount Is a Weak Driver of Returns**  
Correlation between discount % and returns is only **r = 0.03** (about 0.01 at product level). Return rate moves from 14.2% at a 5% discount to roughly 20% at 16% and above, while undiscounted lines return at 18.9%. Blanket discount cuts would not fix returns.

✅ **December Is a Refund Spike**  
Return rate climbs from 19.3% in September to **32.2% in December**. Q4 holds 34% of orders but **39% of returned orders** (28.0% rate vs 22.0% for Jan to Sep). December alone produced 128 returned orders and the year's highest monthly refund (₹522K).

✅ **19 Products Hold Half of All Refund Value**  
19 of 104 products generate 50% of refund value and 46 generate 80%. The top four are all Electronics: PixelNest Smart Watch, HomeSync Wi-Fi Security Camera, SonicBay Bluetooth Speaker and PulseTech Over-Ear Headphones (about 20% of refund value combined).

✅ **About 42% of Refund Value Is Preventable**  
Damaged, Defective and Wrong Item returns hold **₹1.60M (41.9%)** of refunds. Product Not As Expected and Wrong Size add another 30.9% from information gaps. Only "Changed Mind" is purely preference-driven.

✅ **Late Delivery Returns Are Fast; Damage Returns Are Slow**  
Late Delivery returns average **4.6 days** (47 of 55 within a week), while Damaged Product averages **17.4 days**. Overall, **51% of refund value** arrives after day 14.

✅ **Refund Deductions Recover Almost Nothing**  
69% of return lines carry a 0% deduction. Total deductions recovered only about **₹88K (2.3%)** of pre-deduction refund value.

✅ **Each Channel Fails in Its Own Way**  
Offline Store has an outsized Wrong Item share (18.9% of its returns vs 7.5% to 10.5% elsewhere), Marketplace has the most Damaged Product (19.2%), and Mobile App has about double the Quality Issue share of the Website (16.9% vs 8.3%). Electronics returns fall to 14.0% in Offline Stores vs 25.3% on Marketplace.

✅ **Slower Delivery and Free Shipping Both Correlate with More Returns**  
Return rate rises from 15.9% (delivery within 2 days) to 22.1% (over 6 days). Lines with free shipping return at 19.5% vs 16.3% for lines with a shipping fee.

✅ **Customer Segment Does Not Matter, Repeat Returners Do**  
Consumer (24.4%), Corporate (24.2%) and Home Office (22.2%) behave alike. However, 62% of customers returned at least once, 58 customers returned three or more lines, and the **top 10% of returning customers account for 37.5% of refund value**.

* * *

## ▶ How to Run This Project

1. Clone the repository:

```
git clone https://github.com/aman-theanalyst/Return-Refund-Analysis.git
```

2. Open the `.pbix` file in **Power BI Desktop**.
3. **Interact with the Dashboard:**
   - Use the **Date Range, Category and Sales Channel slicers** on the Executive Summary page.
   - Switch to **Product Diagnostic** and use the **Return Reason** slicer to isolate a root cause.
   - Click **Reset Filters** to return to the default view.
   - Hover over visuals to view tooltips.
4. Open the `.pptx` file for the project narrative and action plan.

* * *

## 💡 Final Recommendations

- **Attack Preventable Fulfilment Failures First (Critical)**  
  Damaged, Defective and Wrong Item returns hold 42% of refund value. Audit packaging and marketplace handling, add pre-dispatch QC for Smart Devices, Audio and Kitchen, and track a vendor scorecard using returned units and refund amount. *Sizing: every 10% reduction in these reasons is worth about ₹160K a year.*
- **Fix Expectation Gaps in Fashion (Critical)**  
  Product Not As Expected and Wrong Size are the top two reasons by units. Improve images, descriptions and specifications; add size guides, a fit finder and a return-feedback loop, starting with Men's Clothing and Footwear (192 Wrong Size units, 17% of all returned units).
- **Run a 19-Product Refund Watchlist (High Priority)**  
  19 products hold 50% of refund value (₹1.91M). Review the Electronics SKUs first and set a quarterly review of return rate against benchmark. *Sizing: every 10% reduction is worth about ₹190K.*
- **Use Ratings as an Early-Warning Trigger (High Priority)**  
  Flag any product whose rolling rating drops to 3 stars or below, and push post-delivery rating prompts to lift coverage above the current 49%.
- **Prepare a Q4 Peak-Season Plan from October (High Priority)**  
  Add extra packaging QC, prominent size guidance and app-checkout prompts for Fashion. *Sizing: bringing Q4 down to the Jan to Sep return rate (22.0%) would avoid about 63 returned orders and roughly ₹0.28M in refunds (assuming proportional refund value).*
- **Do Not Cut Discounts Across the Board (Strategic)**  
  The correlation with returns is minimal. Instead, avoid deep discounts only on specific products already showing high return risk (the red bubbles in the scatter).
- **Fix Channel-Specific Failure Modes (Strategic)**  
  Marketplace: seller packaging standards. Offline Store: pick-and-pack verification. Mobile App: listing and quality review.
- **Make Return Policy Smarter (Strategic)**  
  Use reason- or category-based return windows and inspection steps instead of a blanket cut, since 51% of refund value arrives after day 14 and damage claims surface late. Review deductions for Changed Mind and Wrong Size, where recovery is currently minimal.
- **Add Monitoring for Repeat Returners (Structural)**  
  Create a view of customers with three or more returned lines and their refund share, for review by customer-experience and risk teams. Treat it as monitoring, not automatic action.

**Priority rule:** fix causes that combine **high returned units and high refund amount** first.

**Suggested next steps for the dashboard:** add a Refund Impact % KPI (17.9% today), a Q4/seasonality view, a return-window band visual, a rating-band slicer and drill-through from the heatmap to the product table.

* * *

## 📌 What Makes This Project Stand Out

✔ **Root-Cause Focus:** goes beyond category totals to subcategory × reason clusters.  
✔ **Advanced DAX:** Pareto ranking, MoM arrows, dynamic text KPIs and benchmark-based risk colouring.  
✔ **Clean Star Schema:** surrogate keys and lean fact table for fast, accurate visuals.  
✔ **Evidence-Based Recommendations:** each action is tied to a measured share of returns or refund value.  
* * *

## 👤 Author & Contact

**Aman Singh Negi** | Data Analyst

🔗 [GitHub](https://github.com/aman-theanalyst)  
🔗 [LinkedIn](https://www.linkedin.com/in/aman-singh-negi-482197310)

* * *

## ⭐ If you found this valuable, give this repository a ⭐ and connect with me!
