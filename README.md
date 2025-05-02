# PedalPoint Bike Riders Performance Insights and Revenue Optimization

<br>

### Project Overview

##### Client: PedalPoint Company LTD
##### Project Title: PedalPoint Bike Rider Performance Insights and Revenue Optimization
##### Analyst: David Abe
##### Tools Used: SQL Server Management Studio (SSMS), Power BI, Excel
##### Data Sources: PedalPoint dataset file (bike_share_yr_0.csv, bike_share_yr_1.csv, cost_table.csv)
##### Dashboard Link: Google Drive - Dashboard Visuals


<br>
<br>

### About PedalPoint Company LTD

PedalPoint is a modern urban mobility platform that provides on-demand bike rentals through a flexible, pay-per-ride or membership model. The company uses real-time data to monitor rider activity, optimize operations, and improve pricing strategies. With a focus on efficient service delivery, PedalPoint supports both casual and registered riders across various seasons and times of day.

<BR>
<br>

### Business Objective

To create an interactive and visually intuitive dashboard that presents PedalPoint company LTD’s core performance metrics for informed decision-making. The required metrics include:
- Hourly Revenue Analysis
- Profit and Revenue Trends
- Seasonal Revenue
- Rider Demographics
- Recommendation on raising prices next year
- The dashboard is designed using the company’s color palette and structured for ease of navigation.
- We need a preliminary version Asap, provide an estimated timeline for completion

<BR>
<BR>


## Data Preparation

#### SQL Server Integration and Merging
All yearly data and the cost table were merged using a SQL query:

```SQL
WITH bike_table AS
	(SELECT * FROM bike_yr_0
	UNION ALL
	SELECT * FROM bike_yr_1)
SELECT
	dteday, hr, weekday, mnth, season, B.yr, riders, rider_type, price, COGS,
	(riders * price) AS Revenue,
	(riders * price) - (cogs * riders) AS Profit
	FROM bike_table
LEFT JOIN cost_table C
ON B.yr = C.yr;
```

#### Import Data
- Data validation was performed to confirm data accuracy, consistency, and absence of duplicates
- Imported the dataset into Power BI via SQL Server connection by executing the custom query developed in SSMS
- Created conditional Year column with rules: 0 = 2021, 1 = 2022
- Changed necessary datatypes

<BR>
<BR>
<BR>

## Exploratory Data Analysis
### Date Table
Created a dynamic Date Table to support time intelligence and ensure alignment with the company's data timeframe

```DAX
DateTable = 
ADDCOLUMNS(
    CALENDAR(DATE(2021, 1, 1), DATE(2022, 12, 31)),
    "Year", YEAR([Date]),
    "MonthNumber", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMM"),
    "Day", DAY([Date]),
    "Weekday", WEEKDAY([Date], 1) - 1,
    "WeekdayName", 
        SWITCH(
            WEEKDAY([Date], 1),
            1, "Sun",
            2, "Mon",
            3, "Tue",
            4, "Wed",
            5, "Thu",
            6, "Fri",
            7, "Sat" ),
    "WeekOfMonth", 
        INT((DAY([Date]) + WEEKDAY(DATE(YEAR([Date]), MONTH([Date]), 1), 1) - 2) / 7) + 1 )
```

<BR>
<BR>

### KPI Metrics

The following created DAX measures were implemented:
- Total Revenue
- Total Profit
- Total Cost
- Total Riders
- Profit Margin
- Cost Margin
- Cost per Rider
- Profit per Rider
- Cost per Unit Profit
- Profit per Unit Cost
- Supporting Dax Measures for Trends and Calculations

<BR>
<BR>

### Business Questions Answered
1. Hourly Revenue Analysis: Displayed Average Revenue per Hour across all days and filtered by weekday. Added a Tooltip to also display Profit per Hour alongside Average Revenue, visually represented by Matrix.
2. Riders’ Profit and Revenue Trends: Visualized monthly and yearly using a column chart, with an integrated Tooltip that highlights Rider trends alongside Revenue and Profit, including month-over-month (MoM) differences vs last month.
3. Seasonal Revenue: Grouped Revenue by season (1-4), visually represented with Clustered Bar Chat.
4. Rider Demographics: Categorized riders by rider_type (Casual vs Registered) to analyze their contribution to Total Revenue, visualized using a donut chart.

<BR>
<BR>

### Data Insight Snapshot

| Metric                 | 2021    | 2022    | 2021–2022 Total |
|------------------------|---------|---------|------------------|
| Total Revenue          | $4.96M  | $10.23M | $15.19M          |
| Total Profit           | $3.42M  | $7.03M  | $10.45M          |
| Total Cost             | $1.54M  | $3.20M  | $4.74M           |
| Total Riders           | 1.24M   | 2.05M   | 3.29M            |
| Cost per Unit Profit   | $0.45   | $0.45   | $0.45            |
| Profit per Unit Cost   | $2.22   | $2.20   | $2.20            |
| Cost per Rider         | $1.24   | $1.56   | $1.44            |
| Profit per Rider       | $2.75   | $3.43   | $3.17            |
| Cost Margin            | 31.09%  | 31.27%  | 31.21%           |
| Profit Margin          | 68.91%  | 68.73%  | 68.79%           |

<BR>
<BR>

### Growth and Efficiency Insights (2021 -2022)

1. Strong Revenue and Rider Growth
   - Revenue increased by 106% from $5M to over $10M between 2021 and 2022.
   - Riders increased by 65% from 1.24M to 2.05M, showing strong demand growth and effective market expansion

2. Stable Cost Efficiency
   - Cost per Unit Profit remained constant at $0.45 in both years, reflecting how the company maintained stable cost efficiency.
   - Profit per Unit Cost (($2.22 vs $2.20)) only slightly declined by $0.02 between 2021 and 2022, which indicate strong operational discipline and scalability.

3. Strong Unit Profitability and Stable Margins Maintained During Expansion (Between 2021 and 2022):
   - Cost per Rider increased from $1.24 to $1.56 (a 26% rise), reflecting the natural operational cost growth associated with a larger customer base.
   - Profit per Rider also rose from $2.75 to $3.43 (a 25% gain), showing that profitability per customer improved despite higher servicing costs.
   - At the same time, the overall Profit Margin remained highly stable around 69%, while the Cost Margin stayed consistent at 31% across both years. Reflecting strong operational discipline and scalable growth. 

4. Strong Unit Economics Maintained. In a combined period of 2021–2022:
   - For every $1 spent on cost, it generated approximately $2.20 in profit, reflecting highly efficient use of operational spending.
   - With a cost of $1.44 per rider and a corresponding profit of $3.17 per rider, the business consistently delivered high returns on cost and strong profitability per customer, reinforcing the scalability and financial efficiency of its model.


<BR>
<BR>

### Strategic Recommendation For Price Increase in 2023

#### 1. Rider Demographics:
- Registered Riders : make up 81% (2,672,662) of the Total Base riders. These are the company's loyal and long-term users.
- Casual Riders :  make up the remaining 19% (620,017). These users are less frequent and can be more price-tolerant.

#### The Recommendation:
- Apply full 7% increase to Casual riders only.
- Keep Registered rider pricing unchanged to protect loyalty and engagement.

<BR>

#### 2. Revenue By Seasonal Demand
Revenue by season:

- Season 1:  $2.2M
- Season 2:  $4.2M
- Season 3:  $4.9M
- Season 4:  $3.9M

<BR>

>  Season 2 and Season 3 generate 59% of Total Revenue.
> 
>  These are company's peak demand periods.

<BR>


#### The Recommendation:
- Dynamic seasonal pricing, apply 5-6% increase during Season 2 and Season 3 for both rider types.
- Leave pricing unchanged in Season 1 and Season 4.


<BR>
<BR>
<BR>


### Timeline Estimate
Based on working day format:

#### Day 1:
- Run SQL query to merge bike_share and cost tables
- Connect and import dataset from SQL Server into Power BI
- Perform initial data inspection and structure confirmation


#### Day 2:
- Perform data cleaning and transformations in Power Query
- Create and configure a custom Date Table
- Validate data types, remove duplicates, and apply transformations


#### Day 3:
- Build core KPIs (Revenue, Profit, Riders, Cost, etc.)
- Create supporting DAX measures for trends and calculations
- Start dashboard layout design and theming with company colors


#### Day 4:
- Finalize data visuals (Hourly Revenue, Seasonal Trends, Demographics)
- Add tooltips, slicers, and interactivity features
- Test filter logic and date selections


#### Day 5-6:
- Advice and Recommendation
- Polish and review dashboard


#### Day 7
- Export visuals for reporting
- Deliver final dashboard and documentation

<BR>
<BR>


