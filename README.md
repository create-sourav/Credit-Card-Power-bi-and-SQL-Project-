# Credit Card Analytics — Power BI + SQL

## 📊 Dashboard Previews

### Credit Card Customer Report
![Credit Card Customer Report](Screenshot%202025-10-18%20204656.png)

### Credit Card Transaction Report
![Credit Card Transaction Report](Screenshot%202025-10-18%20204714.png)



This repository contains an end-to-end Credit Card Transaction Analytics project built using Power BI and SQL. The project focuses on real-time KPI monitoring, transaction trend analysis, customer behavior insights, and performance optimization.The goal of this project is to analyze large volumes of credit card transaction data and build an interactive, business-ready Power BI dashboard with SQL-powered data cleaning, modeling, and optimization.

---

## Contents of this repository

* `credit card project sql+power bi.pbix` — Power BI Desktop report file (primary deliverable).
* `README.md` — This file.
* `screenshots/` — Folder that should contain the dashboard screenshots (place the two `.png` screenshots here). Reference them in the README so they appear on GitHub.
* `sql/` — (Recommended) SQL scripts to create tables and load sample data. **Add your own** `create_tables.sql` and `load_data.sql` here.
* `data/` — (Optional) Sample CSVs if you prefer local refresh instead of SQL connections.

---

## Project overview & objectives

Monitor real-time KPIs (revenue, transaction count, fraud indicators, average ticket size).

Analyze daily/weekly/monthly trends in transaction volume.

Study customer segments based on spending behavior.

Detect transaction anomalies or unusual spikes.

Track merchant category performance.
The visual design prioritizes a single-page executive dashboard with supporting visualizations for segmentation analysis and trend analysis.

---

## Key measures (DAX examples)

Below are the core measures included in the report — you can paste these into your PBIX if you recreate the model:

```dax
Total Revenue = SUM('transactions'[Revenue])
Total Interest Earned = SUM('transactions'[Interest_Earned])
Total Amount = SUM('transactions'[Amount])
Transaction Count = COUNTROWS('transactions')

Current Week Revenue =
CALCULATE(
    [Total Revenue],
    'dim_date'[Week_Start_Date] = MAX('dim_date'[Week_Start_Date])
)

Previous Week Revenue =
CALCULATE(
    [Total Revenue],
    'dim_date'[Week_Start_Date] = MAX('dim_date'[Week_Start_Date]) - 7
)

Week Over Week % = DIVIDE([Current Week Revenue] - [Previous Week Revenue], [Previous Week Revenue])
```

Adjust field and table names to match your model.

---

## Visuals and Layout (what's on the dashboard)

Key sections shown in the screenshots:

* Top KPI cards: Income, Interest, Revenue.
* Time selector (Week_Start_Date) and quarter/quarter tiles
* Trend chart: Revenue over time by month/day and by gender
* Bar charts: Revenue by Age Group, Income Group, Top States, Dependents, Education, Job
* Table: QTR revenue and total transaction count by Card_Category (and other metrics)
* Clustered bars for Revenue by Use_Type and Revenue by Expenditure

(Refer to screenshot files in `/screenshots` for the exact layout.)

---

## How to run / open the report

1. **Prerequisites**

   * Power BI Desktop (latest stable release recommended).
   * Access to the SQL Server (or SQL-compatible) data source if the PBIX is configured for DirectQuery or live connection.

2. **Open file**

   * Download `credit card project sql+power bi.pbix` and open it in Power BI Desktop.

3. **Update data source**

   * In Power BI Desktop: `Home` → `Transform data` → `Data source settings` → `Change Source...` to update the connection string to your SQL Server (or point to local CSV files).
   * For SQL Server: typical connection string parameters are `Server=YOUR_SERVER_NAME; Database=YOUR_DB; Trusted_Connection=True` (or use SQL auth user/password form in Power BI UI).

4. **Refresh data**

   * After updating the connection, click `Refresh` to pull data.
   * If credentials are needed, Power BI will prompt — supply appropriate authentication.

---

## Recommended SQL structure (example)

Create two simple tables to match the model above: `customers` and `transactions`. Add a `dim_date` table (you can generate this in MySQL).

Example (very simplified):

```Mysql
CREATE DATABASE creditcard_db;

use creditcard_db;
DROP TABLE IF EXISTS customer_detail;
drop table if exists creditcard_detail;
CREATE TABLE creditcard_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date DATE,
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);




CREATE TABLE customer_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);

USE creditcard_db;


LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/cc_add.csv'
INTO TABLE creditcard_detail
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\r\n' IGNORE 1 LINES
(Client_Num, Card_Category, Annual_Fees, Activation_30_Days, Customer_Acq_Cost, @d, Week_Num, Qtr, current_year, Credit_Limit, Total_Revolving_Bal, Total_Trans_Amt, Total_Trans_Ct, Avg_Utilization_Ratio, Use_Chip, Exp_Type, Interest_Earned, Delinquent_Acc)
SET Week_Start_Date = STR_TO_DATE(@d, '%d-%m-%Y');

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/cust_add.csv'
INTO TABLE customer_detail
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 LINES;

select * from customer_detail;
select *from creditcard_detail;

```

Place actual load scripts in the `/sql` file.

---

## Publish to Power BI Service (optional)

1. Save the PBIX.
2. `Home` → `Publish` and sign into your Power BI tenant.
3. Choose a workspace and publish. Configure scheduled refresh if you want automated updates (you'll need a gateway for on-prem SQL).

---

## What to include in this GitHub repo (recommended)

* `credit card project sql+power bi.pbix` (the PBIX) — **required** for sharing the report.
* `sql/create_tables.sql` — table creation scripts.
* `sql/load_sample_data.sql` — scripts to load sample data (or point to CSVs in `/data`).
* `screenshots/` — two dashboard screenshots. Put them in `/screenshots` and reference as `screenshots/dashboard1.png` to make them visible in README.
* `LICENSE` — choose an appropriate license (MIT recommended for open-source analytics projects) and add it.

---

## Tips & troubleshooting

* If visuals show blanks after opening the PBIX, update the data source and refresh.
* If you use an on-prem SQL Server and plan to publish, configure an Enterprise Gateway for scheduled refresh.
* Keep column / table names consistent between SQL and Power BI or update DAX measures accordingly.

 the data to be stored as CSV or in a database.
