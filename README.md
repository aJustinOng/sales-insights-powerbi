# Sales Insights using PowerBI ![PowerBI](./assets/img/powerbi.svg)

**Skills:** `SQL | DAX | Data Visualization`

**Tools:** `MySQL Workbench | Microsoft Excel | PowerBI`

##### [See my other projects!](https://github.com/aJustinOng)

---

## Overview

This project is based on CodeBasic's [PowerBI | Sales Insights Data Analysis](https://www.youtube.com/playlist?list=PLeo1K3hjS3uva8pk1FI3iK9kCOKQdz1I9) project.

In this data analysis project, I took a sales dataset and used it to create a dashboard in PowerBI. I first used MySQL Workbench and Excel to clean and ETL a sales dataset. I then used PowerBI to analyze and visualize the revenue and profit across different regions, customers, and markets. I learned to focus on the critical areas (profit rather than revenue, etc.) that a sales manager would be interested in to answer and tackle sales problems.

I used basic DAX to return specific data aggregates that could be used in visualizations. I also learned how to integrate the powerful interactive tools in PowerBI to allow my stakeholders to conveniently isolate data within specific conditions. I also took additional feedback to drastically improve my initial dashboard.

## Table of contents:
1. [Defining a Problem Statement](#1-defining-a-problem-statement)
2. [Data Discovery and Analysis with SQL](#2-data-discovery-and-analysis-with-sql)
3. [Data Cleaning and ETL](#3-data-cleaning-and-etl)
4. [Dashboarding with PowerBI](#4-dashboarding-with-powerbi)
5. [Updating Dashboard based on Feedback](#5-updating-dashboard-based-on-feedback)
6. [Summary](#summary)

---

## 1. Defining a Problem Statement

The problem statement is a hypothetical one, used to simulate a real-life problem that a company might encounter.

AtliQ Hardware is a computer hardware manufactoring company that supplies their products to many clients across India. The sales director of AtliQ is currently facing an issue: the market is growing dynamically, and he has trouble tracking the sales and insights of the business across the North, South, and Central regions.

Whenever he would call the regional directors, they would tell him basic insights (last quarter's sales, future prospects, etc.) but when asked to provide relevant data, they would send him a dump of Excel files that are hard to understand. The sales director is frustrated with the situation as he sees that the overall sales are declining, and suspects that his regional managers are lying or sugarcoating their reports. He wants to know specific areas and issues that he can tackle to drive up sales.

So the sales director hires me, a data analyst, to create a straightforward, digestable PowerBI dashboard that he can use to visualize the insights of the business. He wants to be able to pull up the dashboard with the current data and be able to make data-driven decisions to increase the company's sales.

## 2. Data Discovery and Analysis with SQL

The very first step in this project was to use MySQL Workbench to import the data that was in the `db_dump_version_2.sql` file. I set up a local MySQL server to open the .sql file. I was met with five tables: `customers`, `date`, `markets`, `products`, and `transactions`. This database uses the Star Schema data model, where `transactions` was the fact table and the rest were dimension tables (see image below).

<img src="/assets/img/databaseStarSchema.png" width="100%"/>

From here, I could freely use MySQL Workbench to query data for analysis and validation. Here are some queries I used:

---

### SQL Queries

<br>

Get a total count of the number of transactions in the `transactions` table:

`SELECT COUNT(*) FROM transactions`

<br>

Get the sum of the sales from `transactions`:

`SELECT SUM(transactions.sales_amount) FROM transactions`

<br>

Select all customers from `customers` with names that start with 'S':

`SELECT * FROM customers WHERE customer_name LIKE 'S%'`

<br>

Select all transactions in `transactions` where the year is 2020 in `date`:

`SELECT transactions.* FROM transactions INNER JOIN date ON transactions.order_date=date.date WHERE date.year=2020`

---

<img src="/assets/img/mySQLWorkbenchQuery.png" width="100%"/>

## 3. Data Cleaning and ETL

No raw dataset comes without any problems. I mainly used PowerBI's transform tool to locate and clean up the errors in the dataset.

> Here I ran into a critical issue. PowerBI could not connect to my local MySQL server. Despite multiple reinstallations of different versions of the MySQL connector and server, I could not get it to work. As this was not the focus of the project, I decided to export the dataset from MySQL Workbench in a .csv format and further convert that into a Excel file. I provided the file in the repo as `sales.xlsx`.

After importing the sales dataset into PowerBI, I had two main goals: clean up irrelevant data and convert the currency values from Indian Rupee (₹) to U.S. Dollar ($). First, several tables incorrectly had their headers as values, so I used DAX to promote the first row of values to the headers.

`= Table.PromoteHeaders(transactions_Sheet, [PromoteAllScalars=true])`

Next I noticed some null values. Null does not mean zero or negative but rather nothing, so I filtered them out. 

`= Table.SelectRows(#"Changed Type", each ([sales_qty] <> null))`

Since I was more familar with the U.S. Dollar, I used DAX convert Indian Rupee (₹) to U.S. Dollar ($) as the current exchange rate. I converted the values from the `sales_amount` column and put them into a new column called `sales_amount_in_usd`.

`= Table.AddColumn(#"Changed Type", "sales_amount_in_usd", each if [currency] = "INR" then [sales_amount]/86 else [sales_amount])`

<img src="/assets/img/powerBITransform.png" width="100%"/>

I did the same currency conversion for the `profit_margin` and `cost_price` columns. There were other minor cleanups and type conversions but these were the main ones.

## 4. Dashboarding with PowerBI

After cleaning, the next step was dashboarding or visualizing the data. In my first iteration, I focused on sales revenue and quantity, creating several graphs based on the markets and trends, as seen in the image below: 

<img src="/assets/img/dashboard-0.png" width="100%"/>

I included the revenue of the top 5 customers and products, along with a year and month filter. All the visualizations in PowerBI are automatically interactive, which means that the user can click on a specific market, customer, or time and the entire dashboard will change accordingly.

However, the first version of a dashboard is usually not the final version.

## 5. Updating Dashboard Based on Feedback

To simulate a real-life scenario of taking and implementing feedback, I used the feedback comments on the YouTube video to improve my dashboard. I ended up with two dashboards, one more focused on the profit analysis and the other on the performance of regions or markets.

> Initially, the first version of the dataset (not `db_dump_version_2.sql`) did not include profit margins and cost prices, so I had to update PowerBI with the `db_dump_version_2.sql`. This meant I had to restart the project with this new dataset, where I recorded the previous steps. It was not a lot of difference between the two datasets other than some errors that were neutralized during the cleaning section and of course, the `profit_margin_percentage`, `profit_margin`, and `cost_price` columns.

The first dashboard showed more insights into the actual profit of each market instead of the revenue, which can be deceiving. Some feedback were concerning with that, where they wanted insights on revenue contribution %, profit margin %, and profit margin contribution %. I used DAX to calculate their values:

Revenue Contribution %:

`Revenue Contribution % = DIVIDE([Revenue],CALCULATE([Revenue],ALL('products'),ALL('customers'),ALL('markets')))`

Profit Margin %:

`Profit Margin % = DIVIDE([Total Profit Margin], [Revenue], 0)`

Profit Margin Contribution %:

`Profit Margin Contribution % = DIVIDE([Total Profit Margin],CALCULATE([Total Profit Margin],ALL('products'),ALL('customers'),ALL('markets')))`

I also converted the graphs on the bottom right into a table, where the user can easily sort each value by ascending or descending order to determine which customers have the best/worst revenue contribution %, profit margin %, or profit margin contribution %.

<img src="/assets/img/dashboard-1.png" width="100%"/>

The second dashboard focused more on the individual markets. I added a profit % slider that highlights underperforming markets red. I also created two pie charts that displayed the sales quantity and profit margins of the North, Central, and South regions. I analyzed a disparity between those two values in the regions, so I determined that a pie chart was the simplest way to do so.

I also converted the revenue trend chart into a clustered column chart to show the difference of the revenue of the selected time frame compared to the revenue of the previous year. The red line represented the profit margin %. This is what the dashboard looks like with the year set to 2020.

<img src="/assets/img/dashboard-2.png" width="100%"/>

## Summary

In this Sales Insights using PowerBI project, I utilized MySQL Workbench, Excel, and PowerBI to clean, ETL, and visualize a sales dataset. I used SQL and DAX to query and filter data respectively. I learned the basics of the responsibilities of a data scientist that was presented with a problem statement and data, and required to create a solution (a dashboard).

I spent most of the project's time on PowerBI, using its powerful transforming and dashboarding tools to produce the final result. I also took additional feedback to drastically improve my initial dashboard.

[See my other projects!](https://github.com/aJustinOng)
