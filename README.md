# PedalPoint Performance Insights and Revenue Optimization

<p align="center">
<img height="400" alt="Home Dashboard" src="https://github.com/user-attachments/assets/a9b30ee1-b588-4e06-9c88-3f5431ea7aed" />
</p>

<br>

### Project Overview

##### Client: PedalPoint LTD
##### Project Title: PedalPoint Performance Insights and Revenue Optimization
##### Analyst: David Abe
##### Tools Used: SQL Server Management Studio (SSMS), Power BI, Excel
##### Data Sources: [PedalPoint dataset file](https://drive.google.com/drive/folders/1SwM1JgknA4KKNum7R8vQZONQb4BhDNXb?usp=sharing)



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
<img width="200" alt="Total Revenue" src="https://github.com/user-attachments/assets/b416bcc3-8b6a-4b68-91bf-680232d38631" />

This metric represents the total income generated from bike rides by multiplying the number of riders by the price per ride. It reflects the gross earnings before subtracting operational costs. It is a primary indicator of business scale and growth over time.

```Dax
Total Revenue = SUM(Query1[Revenue])
```
<br>

- Total Profit
<img width="200" alt="Total Profit" src="https://github.com/user-attachments/assets/19c61206-33de-4a15-ba90-40a2d676ea31" />

Total Profit is the net gain after subtracting the total cost (COGS) from total revenue. In this case, we already did the calculation inside SSMS to generate Profit Column. It shows how much money the company actually retains from its operations and is key to assessing the company’s financial health.

```Dax:
Total Profit = SUM(Query1[Profit])
```
<br>

- Total Cost
<img width="200" alt="Total Cost" src="https://github.com/user-attachments/assets/430f0f27-c65f-4100-86af-61d78c818ad1" />

This reflects the cumulative operational expenses (Cost of Goods Sold) associated with each ride. It includes all costs tied to providing the service and is used to calculate profitability and efficiency.

```Dax:
Total Cost = SUM(Query1[Revenue]) - SUM(Query1[Profit])
```
<br>

- Total Riders
<img width="200" alt="Riders" src="https://github.com/user-attachments/assets/4d0b0179-3290-4294-84cb-cf9831d29b4a" />

This shows the total number of users who rented bikes over the selected period. It’s a critical metric to evaluate customer reach, engagement, and demand trends.

```Dax:
Total Rider = SUM(Query1[riders])
```
<br>

- Profit Margin
<img width="200"  alt="Total Profit x profit margin" src="https://github.com/user-attachments/assets/2d0c8f2c-2812-4aa6-be9a-02e14b773e8f" />

This ratio (Profit ÷ Revenue) expresses the percentage of revenue retained as profit. A high margin indicates efficient cost management and good pricing strategy. It remained stable at 68.8% across both years in the analysis.

```Dax:
Profit Margin = FORMAT(DIVIDE(SUM(Query1[Profit]), SUM(Query1[Revenue])), "0.00%")
```
<br>

- Cost Margin
<img width="200" height="140" alt="Total Cost x cost margin" src="https://github.com/user-attachments/assets/2bc50471-aa32-4055-99d8-926250671512" />

Calculated as (Cost ÷ Revenue), this shows the portion of revenue consumed by costs. In this case, the cost margin hovered around 31.2%, indicating strong operational efficiency.

```Dax:
Cost Margin = FORMAT(DIVIDE([Total Cost], SUM(Query1[Revenue])), "0.00%")
```
<br>

- Cost per Rider
<img width="200" alt="Cost per Rider" src="https://github.com/user-attachments/assets/20b7cff3-0f3d-40b7-aba2-94718b3f1e4c" />

The average cost incurred to serve each individual rider. This helps evaluate whether operations are scaling efficiently with rider growth. 

```Dax:
Cost per Rider = DIVIDE((SUM(Query1[Revenue]) - SUM(Query1[Profit])), SUM(Query1[riders]))
```
<br>

- Profit per Rider
<img width="200" alt="Profit per Rider" src="https://github.com/user-attachments/assets/e2a498e1-f373-49c5-8b66-bd6a6228d49e" />

The net earnings per rider after accounting for cost. It demonstrates how valuable each customer is to the business. Shows profit earn per user.

```Dax:
Profit per Rider = DIVIDE(SUM(Query1[Profit]), SUM(Query1[riders]))
```
<br>

- Cost per Unit Profit
<img width="200" alt="Cost per Unit of Profit" src="https://github.com/user-attachments/assets/4620c8de-028c-463e-8162-37715785111a" />

Shows how much cost is required to generate $1 profit. A lower value indicates higher profitability. It remained stable at $0.45, signaling operational consistency.

```Dax:
Cost per Unit Profit = DIVIDE((SUM(Query1[Revenue]) - SUM(Query1[Profit])), SUM(Query1[Profit]))
```
<br>

- Profit per Unit Cost
<img width="200" alt="Profit per Unit Cost" src="https://github.com/user-attachments/assets/0c1b5ea5-5e00-4b28-a5ea-58e26d4300ba" />

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
1. **Hourly Revenue Analysis:** Displayed Average Revenue per Hour across all days and filtered by weekday. A Tooltip to also display Profit per Hour alongside Average Revenue, visually represented by Matrix. <br>

<p align="center">
	<img width="25%" alt="Revenue per Hour" src="https://github.com/user-attachments/assets/2715ee64-55c4-4884-a074-fb4ec17fcb2a" />
	<b style="margin: 0 50px; font-size: 20px;"> vs </b>
	<img width="40%" alt="Revenue per Hour x Tooltip" src="https://github.com/user-attachments/assets/5c19c3e3-4a2e-405a-bf8a-cc477049c070" /> 
</p>	

<br>

- Explore Metrics <br>
<p align="center">
  <img src="https://github.com/user-attachments/assets/ab422d5f-7c79-4098-a885-b97e7b18d354" alt="Revenue per Hour Explore Dashboard" width="45%" />
  <b style="margin: 0 30px; font-size: 20px;">vs</b>
  <img src="https://github.com/user-attachments/assets/eb1a2366-eafd-45cf-bef2-6c51c0e616c8" alt="Revenue per Hour Explore with Tooltip" width="45%" />
</p>




<br>
<br>

2. **Riders’ Revenue and Profit Trends:** Visualized monthly and yearly using a column chart, with an integrated Tooltip that highlights Rider trends alongside Revenue and Profit, including month-over-month (MoM) differences vs last month. <br>

<p align="center">
	<img width="700" alt="Riders' Profit and Revenue Trends" src="https://github.com/user-attachments/assets/5d549946-7510-45e4-af8b-a990159bd57c" />
</p>

<br>

- Explore Metric

<p align="center">
  <img alt="Riders over Time Explore Dashboard" src="https://github.com/user-attachments/assets/d5efb5cc-b5cc-4135-bcab-e49ddadab384" width="45%" />
  <b style="margin: 0 30px; font-size: 20px;">vs</b>
  <img alt="Riders over Time Explore Page x Tooltip" src="https://github.com/user-attachments/assets/bad1854f-2229-4b45-9e20-ea2fc946c5c4" width="45%" />
</p>

<br>
<br>

3. **Seasonal Revenue:** Grouped Revenue by season (1-4), visually represented with Bar Chat.

<p align="center">
	<img width="309" height="226" alt="Revenue per Season" src="https://github.com/user-attachments/assets/74331a7e-1129-4eb6-95e8-a22a7bb6c738" />
</p>

<br>
<br>

4. **Rider Demographics:** Categorized riders by rider_type (Casual vs Registered) to analyze their contribution to Total Revenue, visualized using a donut chart.

<P ALIGN="CENTER">
	<img width="314" height="239" alt="Rider Demographics" src="https://github.com/user-attachments/assets/c7bfa95c-16ea-4047-9d4f-a93101ad9c08" />
</P>


<BR>
<BR>

## Efficiency Insights and Price Recommendation

#### Data Insight Snapshot

| Metric                 | 2021      | 2022     | Total			   |
|:-----------------------|:----------|:-------- |:-----------------|
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
<br>

