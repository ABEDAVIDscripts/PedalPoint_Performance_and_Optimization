# PedalPoint Bike Riders Performance Insights and Revenue Optimization

<br>

### 📅 Project Overview

##### Client: Toman Company LTD
##### Project: Toman Bike Rider Performance Insights and Revenue Optimization
##### Analyst: [Your Name]
##### Tools Used: SQL Server Management Studio (SSMS), Power BI, Excel
##### Data Sources: bike_share_yr_0.csv, bike_share_yr_1.csv, cost_table.csv
##### Dashboard Link: Google Drive - Dashboard Visuals
##### Timeline: 

<br>

### 🏢 About PedalPoint Company LTD

PedalPoint is a modern urban mobility platform that provides on-demand bike rentals through a flexible, pay-per-ride or membership model. The company uses real-time data to monitor rider activity, optimize operations, and improve pricing strategies. With a focus on efficient service delivery, PedalPoint supports both casual and registered riders across various seasons and times of day.

<BR>

### 📊 Business Objective

To create an interactive and visually intuitive dashboard that presents PedalPoint company LTD’s core performance metrics for informed decision-making. The required metrics include:
- Hourly Revenue Analysis
- Profit and Revenue Trends
- Seasonal Revenue
- Rider Demographics
- Recommendation on raising prices next year
- The dashboard is designed using the company’s color palette and structured for ease of navigation.

<BR>

### ⚙️ Data Preparation

##### SQL Server Integration and Merging
All yearly data and the cost table were merged using the following SQL query:

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

##### Import Data
Imported the dataset into Power BI via SQL Server connection by executing the custom query developed in SSMS

##### Cleaning Steps
- Created conditional Year column with rules: 0 = 2021, 1 = 2022
- Changed necessary datatypes
- 

<BR>
