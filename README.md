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
- The dashboard should be designed using the company’s color palette and structured for ease of navigation.
- Provide a preliminary version Asap, also to provide an estimated timeline for completion

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

### KPI Measures Implemented

The following created DAX measures were implemented:
- Total Revenue

This metric represents the total income generated from bike rides by multiplying the number of riders by the price per ride. It reflects the gross earnings before subtracting operational costs. It is a primary indicator of business scale and growth over time.

```Dax
Total Revenue = SUM(Query1[Revenue])
```
<br>

- Total Profit

Total Profit is the net gain after subtracting the total cost (COGS) from total revenue. In this case, we already did the calculation inside SSMS to generate Profit Column. It shows how much money the company actually retains from its operations and is key to assessing the company’s financial health.

```Dax:
Total Profit = SUM(Query1[Profit])
```
<br>

- Total Cost

This reflects the cumulative operational expenses (Cost of Goods Sold) associated with each ride. It includes all costs tied to providing the service and is used to calculate profitability and efficiency.

```Dax:
Total Cost = SUM(Query1[Revenue]) - SUM(Query1[Profit])
```
<br>

- Total Riders

This shows the total number of users who rented bikes over the selected period. It’s a critical metric to evaluate customer reach, engagement, and demand trends.

```Dax:
Total Rider = SUM(Query1[riders])
```
<br>

- Profit Margin

This ratio (Profit ÷ Revenue) expresses the percentage of revenue retained as profit. A high margin indicates efficient cost management and good pricing strategy. It remained stable at 68.8% across both years in the analysis.

```Dax:
Profit Margin = FORMAT(DIVIDE(SUM(Query1[Profit]), SUM(Query1[Revenue])), "0.00%")
```
<br>

- Cost Margin

Calculated as (Cost ÷ Revenue), this shows the portion of revenue consumed by costs. In this case, the cost margin hovered around 31.2%, indicating strong operational efficiency.

```Dax:
Cost Margin = FORMAT(DIVIDE([Total Cost], SUM(Query1[Revenue])), "0.00%")
```
<br>

- Cost per Rider

The average cost incurred to serve each individual rider. This helps evaluate whether operations are scaling efficiently with rider growth. 

```Dax:
Cost per Rider = DIVIDE((SUM(Query1[Revenue]) - SUM(Query1[Profit])), SUM(Query1[riders]))
```
<br>

- Profit per Rider

The net earnings per rider after accounting for cost. It demonstrates how valuable each customer is to the business. Shows profit earn per user.

```Dax:
Profit per Rider = DIVIDE(SUM(Query1[Profit]), SUM(Query1[riders]))
```
<br>

- Cost per Unit Profit

Shows how much cost is required to generate $1 profit. A lower value indicates higher profitability. It remained stable at $0.45, signaling operational consistency.

```Dax:
Cost per Unit Profit = DIVIDE((SUM(Query1[Revenue]) - SUM(Query1[Profit])), SUM(Query1[Profit]))
```
<br>

- Profit per Unit Cost

This reflects the profit earned per dollar spent. It’s a profitability efficiency metric. A value of $2.20 means every $1 spent returns $2.20 in profit. A strong indicator of business performance.

```Dax:
Profit per Unit Cost = DIVIDE(SUM(Query1[Profit]), (SUM(Query1[Revenue]) - SUM(Query1[Profit])))
```
<br>

- Supporting Dax Measures for Trends and Calculations

Custom DAX measures were developed to calculate core KPIs (e.g., Total Revenue, Profit, Riders, Costs) and support advanced analytical capabilities

<BR>
<BR>

### About Metrics and Business Questions Answered
1. **Hourly Revenue Analysis:** Displayed Average Revenue per Hour across all days and filtered by weekday. Added a Tooltip to also display Profit per Hour alongside Average Revenue, visually represented by Matrix.

2. **Riders’ Profit and Revenue Trends:** Visualized monthly and yearly using a column chart, with an integrated Tooltip that highlights Rider trends alongside Revenue and Profit, including month-over-month (MoM) differences vs last month.

3. **Seasonal Revenue:** Grouped Revenue by season (1-4), visually represented with Clustered Bar Chat.

4. **Rider Demographics:** Categorized riders by rider_type (Casual vs Registered) to analyze their contribution to Total Revenue, visualized using a donut chart.

<BR>
<BR>

## Efficiency Insights and Price Recommendation

#### Data Insight Snapshot

| Metric                 | 2021      | 2022     | Total 	   |
|------------------------|-----------|--------- |------------------|
| Total Revenue          | $4.96M    | $10.23M  | $15.19M          |
| Total Profit           | $3.42M    | $7.03M   | $10.45M          |
| Total Cost             | $1.54M    | $3.20M   | $4.74M           |
| Total Riders           | 1.24M     | 2.05M    | 3.29M            |
| Cost per Unit Profit   | $0.45     | $0.45    | $0.45            |
| Profit per Unit Cost   | $2.22     | $2.20    | $2.20            |
| Cost per Rider         | $1.24     | $1.56    | $1.44            |
| Profit per Rider       | $2.75     | $3.43    | $3.17            |
| Cost Margin            | 31.09%    | 31.27%   | 31.21%           |
| Profit Margin          | 68.91%    | 68.73%   | 68.79%           |

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

Revenue by season (Registered Riders only):
- Season 1: $1.9M
- Season 2: $3.3M
- Season 3: $3.8M
- Season 4: $3.3M

> Season 2, Season 3 and Season 4 generate 85% of Registered riders’ Total Revenue ($12.3M).
> 
> These are company's peak demand periods.

<BR>

#### The Recommendation:
- Dynamic seasonal pricing. Apply 5-6% increase during Season 2, Season 3 and Season 4 for Registered rider types.
- Leave pricing unchanged in Season 1.

<BR>

### What To Consider During This Stage?
1. Communication Strategy:
	- Make sure pricing updates are communicated properly, especially to Registered Riders who are facing a seasonal increase for the first time.
	- Offer loyalty rewards, bundle benefits, or advance notices to ease adoption.

2. Behavioral Monitoring:
	- After implementation, monitor Registered Rider usage during peak seasons to track any negative reaction early (reduced rides, cancellation of memberships, etc.).

<BR>
<BR>


## Timeline Estimate
Based on working day format:

#### Day 1:
- Merge datasets in SQL
- Import into Power BI
- Initial data inspection and structure confirmation


#### Day 2:
- Clean and transform in Power Query
- Create and configure a custom Date Table
- Validate data types and structures
- Data Modelling


#### Day 3-5:
- Build KPIs & DAX Measures
- Design layout using brand colors
- Start dashboard layout design and theming with company colors
- Create metrics to answer business objectives


#### Day 6:
- Add interactivity, tooltips and other supporting dax measures
- Add visuals
- Test filter logic and date selections


#### Day 7–10:
- Polish dashboard
- Generate insights
	

#### Day 10-12:
- Final overall review
- Export visuals
- Deliver dashboard & documentation

<BR>
<BR>

**Refer to the PedalPoint_documentation for detailed visual and written documentation.**

<br>

