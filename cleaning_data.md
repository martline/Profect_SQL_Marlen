What issues will you address by cleaning the data?

1)The unit_price (from analytics) in the data needs to be divided by 1,000,000, as well as productprice (from all_sessions) needs to be divided by 1,000,000

2)time, timeonsite and date (from all_sessions) needed conversion as following: time -> time in hours, timeonsite -> time in minutes,
date -> to the date format (YYYY-MM-DD)

3)date and timeonsite (from analytics table) needed to be converted, date to date format (YYYY-MM-DD), timeonsite -> time in minutes

4)whole column userid from analytics table is missing values, all column contains NULL

Queries:
Below, provide the SQL queries you used to clean your data.

1) 
-- Had to do extra CAST to have decimal points in the result
```SQL
SELECT CAST(CAST(unit_price AS NUMERIC)/1000000 AS NUMERIC(5, 2)) AS unit_price
FROM analytics

SELECT CAST(CAST(productprice AS NUMERIC)/1000000 AS NUMERIC(5, 2)) AS productprice
FROM all_sessions
```
2)```SQL 
SELECT time/60/60 as time_hrs, timeonsite/60 as timeonsite_minutes, TO_DATE(CAST(date AS TEXT), 'YYYYMMDD')
FROM all_sessions
```
3)```SQL
SELECT CAST(timeonsite AS NUMERIC)/60 as timeonsite_minutes, TO_DATE(CAST(date AS TEXT), 'YYYYMMDD')
FROM analytics
```
4)```SQL
SELECT *
FROM analytics
WHERE userid IS NOT NULL
```