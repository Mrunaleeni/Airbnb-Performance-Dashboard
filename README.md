# 🌍 Global Airbnb Performance Dashboard

An interactive Power BI dashboard analyzing global Airbnb listings, pricing behavior, review patterns, and reviewer engagement across 10 major cities — built to uncover market share dynamics, pricing strategy, customer satisfaction drivers, and review distribution patterns.

## 📌 Executive Summary

This project analyzes a global Airbnb listings and reviews dataset spanning **279,712 listings**, **182,000 hosts**, **10 cities**, and **144 property types** to answer a core business question: *what drives listing concentration, pricing, and guest satisfaction across Airbnb's global markets?*

The dashboard combines **Pareto analysis, market share breakdowns, pricing comparisons, review-score heatmaps, and reviewer frequency modeling** to surface insights an analyst would typically deliver to a growth, pricing, or customer-experience team. It demonstrates end-to-end data analytics skills: data modeling, advanced DAX, KPI design, and dashboard storytelling.

📂 **[Download the full .pbix file](https://drive.google.com/file/d/1FuCFmgYSXJ_IF0FYpihiRYMAvi205FBz/view?usp=sharing)** *(hosted externally — file exceeds GitHub's 100MB limit)*

---

## ❓ Problem Statement

Airbnb operates across hundreds of cities with wide variation in listing volume, pricing, and guest satisfaction. Without a consolidated view, it's difficult to answer questions like:

- Which cities drive the majority of listings and reviews?
- Does room type meaningfully affect pricing?
- How has listing growth evolved over time, and where is the market headed?
- Which cities excel or underperform on specific guest satisfaction metrics?
- Do most guests review once, or are reviews concentrated among repeat reviewers?

This project builds a single interactive dashboard to answer these questions using real listing and review-level data.

---

## 🎯 Business Objectives

1. Quantify market concentration across cities using cumulative contribution (Pareto) analysis.
2. Compare average pricing across room types to understand revenue potential by listing category.
3. Track listing growth over time to identify lifecycle stages (introduction, growth, maturity, decline).
4. Benchmark guest satisfaction across cities on six review dimensions.
5. Model reviewer behavior to understand how review volume is distributed across the reviewer base.

---

## 🗂️ Dataset Description

The dataset contains Airbnb listing and review-level records with the following key fields:

| Field | Description |
|---|---|
| `listing_id` | Unique identifier for each listing |
| `host_id` / host info | Host-level attributes (including Superhost status) |
| `city` | Listing's city (10 cities: Paris, New York, Sydney, Rome, Rio de Janeiro, Istanbul, Mexico City, Bangkok, Cape Town, Hong Kong) |
| `room_type` | Entire Place, Private Room, Shared Room, Hotel Room |
| `price` | Nightly listing price |
| `review scores` | Accuracy, Cleanliness, Check-in, Communication, Location, Value |
| `reviewer_id` | Unique identifier per reviewer |
| `review_id` | Unique identifier per review |
| `review dates` | Used for time-series and lifecycle analysis |

---

## 🛠️ Tools Used

- **Power BI Desktop** — dashboard development and visualization
- **DAX (Data Analysis Expressions)** — calculated columns, measures, cumulative logic
- **Data Modeling** — relational structuring of listings and reviews tables
- **Power Query** — data shaping and cleaning

---

## 📊 Dashboard Walkthrough

### Page 1 — Overview
**KPIs:** Total Listings, Total Cities, Total Hosts, Total Property Types, Total Reviews

**Visuals:**
- **Market Share by City (Pareto Chart):** Ranks cities by listing volume with a cumulative % line, showing what share of the total market the top cities represent.
- **Average Price by Room Type:** Compares Entire Place, Private Room, Shared Room, and Hotel Room pricing.
- **Listings Over Time:** A lifecycle trend line (Introduction → Growth → Maturity → Decline → Reinvention → COVID-19) tracking new listings by room type across years.

### Page 2 — Ratings Analysis
**Visuals:**
- **Review Score Heatmap by City:** Six metrics (Accuracy, Check-in, Cleanliness, Communication, Location, Value) compared side-by-side across all 10 cities, with conditional color formatting to highlight strong/weak performers.
- **Overall Rating Ranking:** A toggle view (via bookmarks/buttons) switching between the detailed heatmap and an aggregated overall rating bar chart per city.

### Page 3 — Review Analysis
**Objective:** Understand how reviews are distributed across the reviewer base — are most guests one-time reviewers, or is there a loyal repeat-reviewer segment?

**Visual:** Review Frequency Distribution Pareto Chart — plots the number of reviewers against how many reviews each has left, with a cumulative % line overlaid.

---

## 🧮 DAX Explanation

**1. Reviews per Reviewer** *(Calculated Column)*
Counts how many reviews each individual reviewer has submitted, using `ALLEXCEPT` to preserve the reviewer-level context while ignoring all other filters.
```dax
Reviews per Reviewer =
CALCULATE(
    COUNT(Reviews[review_id]),
    ALLEXCEPT(Reviews, Reviews[reviewer_id])
)
```

**2. Reviewers** *(Measure)*
A simple distinct count of reviewers, used as the base metric for the frequency chart.
```dax
Reviewers = DISTINCTCOUNT(Reviews[reviewer_id])
```

**3. Cumulative Reviewers** *(Measure)*
For each point on the x-axis (reviews-per-reviewer value), counts how many reviewers fall at or below that frequency — the core logic behind the Pareto curve.
```dax
Cumulative Reviewers =
VAR CurrentReviews = MAX(Reviews[Reviews per Reviewer])
RETURN
CALCULATE(
    DISTINCTCOUNT(Reviews[reviewer_id]),
    FILTER(
        ALL(Reviews[Reviews per Reviewer]),
        Reviews[Reviews per Reviewer] <= CurrentReviews
    )
)
```

**4. Total Reviewers** *(Measure)*
The denominator for the cumulative percentage — total distinct reviewers regardless of any filter context.
```dax
Total Reviewers =
CALCULATE(
    DISTINCTCOUNT(Reviews[reviewer_id]),
    ALL(Reviews)
)
```

**5. Cumulative % Review Frequency** *(Measure)*
Divides cumulative reviewers by total reviewers to produce the % line plotted on the Pareto chart.
```dax
Cumulative % Review Frequency =
DIVIDE([Cumulative Reviewers], [Total Reviewers])
```

**6. Show in Review Frequency Chart** *(Calculated Column)*
A display filter that trims extreme outliers (reviewers with fewer than 6 or more than 85 reviews) so the chart's x-axis stays readable without distorting the underlying distribution.
```dax
Show in Review Frequency Chart =
IF(
    Reviews[Reviews per Reviewer] < 6 || Reviews[Reviews per Reviewer] > 85,
    1,
    0
)
```

---

## 💡 Business Insights

**Market Concentration**
- Paris, New York City, and Sydney together account for nearly half of all listings and 48% of total reviews.
- Paris leads both metrics — a likely driver is that hotel room prices in Paris run roughly twice as much as comparable Airbnb listings, pushing more demand toward short-term rentals.

**Pricing**
- Entire Place listings command significantly higher average prices ($673) than Private Rooms ($462), reflecting stronger revenue potential for hosts who list full properties.
- Interestingly, Hotel Room-type listings on Airbnb average even higher ($800), suggesting this category competes directly with traditional hotel inventory.

**Growth & Lifecycle**
- Airbnb listings peaked around 2015, followed by a decline in 2016–2017 tied to tightening local regulations in several markets.
- Despite the listings slowdown, Airbnb became profitable in the second half of 2016, with 2017 marking its first full year of positive income — showing that revenue and listing volume don't move in lockstep.
- A pre-COVID reinvention phase (2018–2019) preceded a sharp COVID-19 disruption from 2020 onward.

**Guest Satisfaction**
- Most cities maintain average ratings above 9.0 across all six review dimensions.
- Mexico City and Rio de Janeiro are the highest-rated cities overall; Hong Kong and Istanbul rate lowest.
- Cleanliness and Value for Money are the two dimensions with the widest variance across cities — the clearest differentiators between top and bottom performers.

**Reviewer Behavior**
- The overwhelming majority of reviewers leave exactly one review.
- Only a small percentage of reviewers are repeat reviewers, and the distribution follows a classic Pareto (80/20-style) pattern — a small segment of highly engaged users accounts for a disproportionate share of total review volume.

---

## 🚀 Project Impact

This dashboard turns a static, listing-level dataset into a decision-ready tool that could inform:
- **Market expansion strategy** (where listing density is low but satisfaction is high)
- **Pricing strategy** (which room types justify premium pricing)
- **Customer experience investment** (which cities/metrics need targeted improvement)
- **Retention/loyalty programs** (understanding the small base of repeat reviewers as a proxy for repeat guests)

It reflects the kind of exploratory-to-executive analysis a Data Analyst is expected to deliver: starting from raw records and ending with clear, business-ready recommendations.

---

## 🔮 Future Improvements

- Add a **geospatial map view** to visualize listing density and price by neighborhood, not just city.
- Incorporate **host-level segmentation** (Superhost vs. non-Superhost performance comparison).
- Build a **seasonality analysis** (price/demand fluctuation by month) if date granularity allows.
- Add **forecasting** (e.g., using Power BI's built-in forecasting or a DAX-based trend model) to project future listing growth.
- Integrate **sentiment analysis** on review text (if raw review text becomes available) to complement the numeric review scores.

---

## 📁 Repository Structure

```
airbnb-performance-dashboard/
│
├── dashboard/
│   └── (Airbnb_Dashboard.pbix hosted externally — see link above)
│
├── images/
│   ├── Overview.png
│   ├── Overall.png
│   └── Detailed.png
│
└── README.md
```

---

## 👤 About Me

Final-year Data Science student passionate about turning raw data into actionable business insights. Actively seeking **Data Analyst, Business Analyst, Product Analyst, and Graduate Analyst** roles.

📧 mrunaleeni111@gmail.com | 
