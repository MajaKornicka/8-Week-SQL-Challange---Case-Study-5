# 8 Week SQL Challange Case Study 5
## 🛒 Data Mart 🛒 
Source of data and questions: https://8weeksqlchallenge.com/case-study-5/



![data mart](https://github.com/user-attachments/assets/9904d59e-0433-4e1f-bd7e-8fba762e51f8)




### 1. Data Cleansing Steps
----------------------------------------------------------------------------------------------------------
```sql
CREATE VIEW clean_weekly_sales AS
SELECT CONVERT(date, week_Date, 5) as date, 
		DATEPART(week, CONVERT(date, week_Date, 5)) as week,
		DATEPART(month,CONVERT(date, week_Date, 5)) as month,
		DATEPART(year, CONVERT(date, week_Date, 5)) as year,
		CASE WHEN segment LIKE '%1%' THEN 'Young Adults'
			 WHEN segment LIKE '%2%' THEN 'Middle Ageds'
			 WHEN segment LIKE '%3%' OR segment LIKE '%4%' THEN 'Retirees'
			 ELSE 'unknown'
		END as age_band,
		CASE WHEN segment LIKE 'C%' THEN 'Couples'
			 WHEN segment LIKE 'F%' THEN 'Families'
			 ELSE 'unknown'
		END as demographic,
		ROUND((CAST(sales AS FLOAT)/CAST(transactions AS FLOAT)),2) as avg_transactions

FROM data_mart.weekly_Sales


SELECT*
FROM clean_weekly_sales
```


<img width="346" alt="tabela clean_weekly_sales_duża" src="https://github.com/user-attachments/assets/9653b948-d6e4-4896-b7ee-c16ada45c09a">


### 2. Data Exploration
----------------------------------------------------------------------------------------------------------

1. What day of the week is used for each week_date value?

```sql

SELECT DISTINCT DATENAME(weekday,date) as day_of_the_week
FROM clean_Weekly_sales

```



**Solution**

|day_of_the_week|
|---------------|
|Monday|

----------------------------------------------------------------------------------------------------------

2. What range of week numbers are missing from the dataset?

```sql
SELECT DISTINCT week AS week_number
FROM clean_weekly_sales
ORDER BY week
```

We have 52 weeks in total in a year, this dataset contains only 24, so 52-24 = 28 weeks are missing.
Range is from 1 to 12 and then from 37 to 52.

**Solution**

|week_number|
|---------------|
|13|
|14|
|...|
|35|
|36|


----------------------------------------------------------------------------------------------------------


3. How many total transactions were there for each year in the dataset?

```sql
SELECT  year, SUM(transactions) as total_transactions
FROM clean_weekly_sales
GROUP BY year
ORDER BY year
```
|year|total_transactions|
|----|--------------|
|2018|346406460|
|2019|365639285|
|2020|375813651|

----------------------------------------------------------------------------------------------------------


4. What is the total sales for each region for each month?

```sql
WITH A AS (

SELECT region, month(CONVERT(date, week_date,5))as month_number,sales
FROM data_mart.weekly_sales
)

SELECT region, month_number, SUM(CAST(sales AS BIGINT)) AS total_sales
FROM A
GROUP BY region, month_number
ORDER BY region, month_number
```
<img width="126" alt="regions_months_sales" src="https://github.com/user-attachments/assets/6c9b511d-fad5-467f-a050-6c6778265c9e">


🛒🛒🛒
