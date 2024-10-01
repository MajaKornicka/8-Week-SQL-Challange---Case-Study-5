# 8 Week SQL Challange Case Study 5
## 🛒 Data Mart 🛒 
Source of data and questions: https://8weeksqlchallenge.com/case-study-5/


<p align="center">
<img src="https://8weeksqlchallenge.com/images/case-study-designs/5.png" alt="Image" width="450" height="450">






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

**Solution**
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

**Solution**

<img width="126" alt="regions_months_sales" src="https://github.com/user-attachments/assets/6c9b511d-fad5-467f-a050-6c6778265c9e">

----------------------------------------------------------------------------------------------------------

5. What is the total count of transactions for each platform?

```sql
SELECT platform, SUM(transactions) AS transactions_count
FROM data_mart.weekly_sales
GROUP BY platform
```
**Solution**

|platform|transactions_count|
|----|--------------|
|Shopify|5925169|
|Retail|1081934227|


----------------------------------------------------------------------------------------------------------


6.  What is the percentage of sales for Retail vs Shopify for each month?

----------------------------------------------------------------------------------------------------------

7. What is the percentage of sales by demographic for each year in the dataset?

```sql
WITH YS AS (
SELECT year, demographic, sales, SUM(CAST(sales AS bigint)) OVER (PARTITION BY year ORDER BY year) as yearly_sum
FROM clean_weekly_sales),

YSD AS (
SELECT year, demographic, SUM(CAST(sales AS BIGINT)) OVER(PARTITION BY demographic, year ORDER BY year) as sales_by_dem,
		ROUND( CAST(SUM(CAST(sales AS BIGINT)) OVER(PARTITION BY demographic, year ORDER BY year)AS FLOAT)
		/ CAST(yearly_sum AS FLOAT) *100,2) as perc
FROM ys)

SELECT *
FROM ysd
GROUP BY perc, sales_by_dem, demographic,year
ORDER BY year

```
**Solution**


<img width="204" alt="demo" src="https://github.com/user-attachments/assets/10cb6d68-beac-41da-b6dc-7f38886499e3">

----------------------------------------------------------------------------------------------------------

8. Which age_band and demographic values contribute the most to Retail sales?

```sql
SELECT age_band, demographic, SUM(CAST(sales AS BIGINT)) AS retail_sales
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY age_band, demographic
ORDER BY 3 DESC
```

**Solution**

<img width="204" alt="retail sales" src="https://github.com/user-attachments/assets/aa51502a-c335-4e36-81ee-41edb33ad235">

----------------------------------------------------------------------------------------------------------

9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

```sql
SELECT year, platform, ROUND(CAST(SUM(CAST(sales AS BIGINT))AS FLOAT) / CAST(SUM(transactions)AS FLOAT),2) as avg_transaction
FROM clean_weekly_sales
GROUP BY year, platform
ORDER BY year, platform
```

**Solution**

<img width="160" alt="avg_trans" src="https://github.com/user-attachments/assets/2dc2b73b-fecc-44b1-b4bb-db67536a7776">





🛒🛒🛒
