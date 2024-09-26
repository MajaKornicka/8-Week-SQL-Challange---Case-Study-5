# 8 Week SQL Challange Case Study 5
## 🛒 Data Mart 🛒 
Source of data and questions: https://8weeksqlchallenge.com/case-study-5/



![data mart](https://github.com/user-attachments/assets/9904d59e-0433-4e1f-bd7e-8fba762e51f8)



------------------------------------------------------------------------------------------
# 1. Data Cleansing Steps

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




# 2. Data Exploration

1. What day of the week is used for each week_date value?

```sql

SELECT DISTINCT DATENAME(weekday,date) as day_of_the_week
FROM clean_Weekly_sales

```



**Solution**

|day_of_the_week|
|---------------|
|Monday|




🛒🛒🛒
