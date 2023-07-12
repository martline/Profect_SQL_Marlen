What are your risk areas? Identify and describe them.

*Data Validation and Integrity: 
In SQL projects, ensuring the accuracy and integrity of the data is crucial. Failing to properly validate 
input data or enforce data integrity constraints can lead to inconsistent or incorrect data. 
Implementing appropriate data validation checks, constraints, and using proper data types can mitigate this risk.

*Query Performance: 
Inefficient queries can impact the performance of the database, leading to slow response times or resource contention. 
Optimizing queries, creating appropriate indexes, and monitoring query performance can help address performance risks in your SQL project.

*Backup and Recovery: 
Inadequate backup and recovery mechanisms can pose a significant risk in SQL projects. Data loss, system failures, or accidental 
deletions can occur without proper backup and recovery processes. Regularly backing up the database, testing the restoration process, 
and maintaining backup copies in secure locations are crucial for mitigating this risk.

*Error Handling and Logging: 
Proper error handling and logging mechanisms are crucial in SQL projects to identify and resolve issues effectively. Inadequate error handling can 
result in unhandled exceptions, data inconsistencies, or system instability. Implementing robust error handling and logging strategies can aid in identifying 
and resolving issues promptly.

QA Process:
Describe your QA process and include the SQL queries used to execute it.

*Checking field values
```SQL
SELECT fullvisitorid
FROM all_sessions
WHERE  country IS NULL or city IS NULL;

SELECT fullvisitorid
FROM all_sessions
WHERE  visitid IS NULL;

SELECT fullvisitorid
FROM all_sessions
WHERE  productprice IS NULL 
	OR v2productname IS NULL 
	OR v2productcategory IS NULL
```

*Check for unique fields
```SQL
SELECT fullvisitorid,
    SUM(1) AS count
FROM all_sessions
GROUP BY 1
HAVING SUM(1) > 1;

SELECT productsku,
    SUM(1) AS count
FROM sales_report
GROUP BY 1
HAVING SUM(1) > 1;

SELECT sku,
    SUM(1) AS count
FROM products
GROUP BY 1
HAVING SUM(1) > 1;
```

*Comparing row counts against averages
```SQL
SELECT ROUND(AVG(row_count),2) AS avg_ct
FROM (
	SELECT DISTINCT * FROM (
		SELECT DISTINCT TO_DATE(CAST(date AS TEXT), 'YYYYMMDD') AS date, COUNT(*) AS row_count FROM all_sessions
		WHERE date IS NOT NULL
		GROUP BY 1
		ORDER BY date DESC
	) AS date_table
) AS new_table;

SELECT *,
    CASE
        WHEN row_count > 41.35 THEN 'Above average row count'::text
        WHEN row_count < 41.35 THEN 'Below average row count'::text
        ELSE row_count::text
    END AS avg_comparison
FROM (
    SELECT DISTINCT
        TO_DATE(CAST(date AS TEXT), 'YYYYMMDD') AS date,
        COUNT(*) AS row_count
    FROM all_sessions
    WHERE date IS NOT NULL
    GROUP BY TO_DATE(CAST(date AS TEXT), 'YYYYMMDD')
    ORDER BY date DESC
) AS data_table;
```

*Calculating percent change
```SQL
SELECT *,
    ROUND(
        CASE
            WHEN LAG(row_count) OVER (ORDER BY row_count DESC) <> 0
            THEN 100 * (row_count::numeric / LAG(row_count) OVER (ORDER BY row_count DESC) - 1)
            ELSE 0
        END, 2
    ) || '%' AS daily_percent_change
FROM (
    SELECT DISTINCT *
    FROM (
        SELECT DISTINCT TO_DATE(CAST(date AS TEXT), 'YYYYMMDD') AS date, COUNT(*) AS row_count 
        FROM all_sessions
        WHERE date IS NOT NULL
        GROUP BY 1
        ORDER BY date DESC
    ) AS new_table
) AS new_table_2
GROUP BY date, row_count;
```
