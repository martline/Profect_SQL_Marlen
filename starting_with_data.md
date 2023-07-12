Question 1: 
Which countries have the most pageviews on the site?

SQL Queries:
```SQL
SELECT ROUND(AVG(a.pageviews), 2) AS avg_page_view, als.country
FROM analytics a
JOIN all_sessions als
ON a.fullvisitorid = CAST(als.fullvisitorid AS NUMERIC)
WHERE country != '(not set)'
GROUP BY als.country
ORDER BY avg_page_view DESC
LIMIT 10
```
Answer: 
"avg_page_view"		"country"
	20.53			"Hong Kong"
	17.09			"Dominican Republic"
	15.96			"Japan"
	14.68			"Taiwan"
	14.16			"United States"
	11.62			"Germany"
	11.47			"Singapore"
	11.07			"Costa Rica"
	10.97			"Belarus"
	10.01			"Canada"


Question 2: 
IN which countries customers spent more time on the site?

SQL Queries:
```SQL
SELECT ROUND(AVG(a.timeonsite), 2) AS avg_time, als.country
FROM analytics a
JOIN all_sessions als
ON a.fullvisitorid = CAST(als.fullvisitorid AS NUMERIC)
WHERE country != '(not set)' AND a.timeonsite IS NOT NULL
GROUP BY als.country
ORDER BY avg_time DESC
LIMIT 10
```
Answer:
"avg_time"		"country"
1291.26			"Taiwan"
918.67			"Belarus"
912.04			"Japan"
877.28			"Hong Kong"
807.53			"Finland"
766.00			"Sint Maarten"
756.74			"United States"
756.21			"Sri Lanka"
716.82			"Egypt"
708.94			"Indonesia"



Question 3: 
How much revenue was accumulated in JULY of 2017?
SQL Queries:
```SQL
WITH revenue_07_2017 AS (
	SELECT TO_DATE(CAST(date AS TEXT), 'YYYYMMDD') AS date, ROUND(SUM(revenue)/1000000, 2) AS total_revenue
	FROM analytics
	WHERE revenue IS NOT NULL 
		AND EXTRACT (YEAR FROM TO_DATE(CAST(date AS TEXT), 'YYYYMMDD')) = '2017' 
		AND EXTRACT (MONTH FROM TO_DATE(CAST(date AS TEXT), 'YYYYMMDD')) = '7'
	GROUP BY date
	ORDER BY date
)

SELECT SUM(total_revenue) as revenue_July_2017
FROM revenue_07_20176
```
Answer:

"revenue_july_2017"
413330.78


