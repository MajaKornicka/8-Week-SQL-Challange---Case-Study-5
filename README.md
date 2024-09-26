# 8 Week SQL Challange Case Study 5
## Data_Mart 🛒 
Source of data and questions: https://8weeksqlchallenge.com/case-study-5/


![Uploading data mart.png…]()



```sql
--                      1. Data Cleansing Steps


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


--                      2. Data Exploration


-- 1. What day of the week is used for each week_date value?



WITH weekday AS (
SELECT DATEPART(weekday, date) as weekday
FROM clean_weekly_sales
)

SELECT weekday, COUNT(weekday)
FROM weekday
GROUP BY weekday

```
