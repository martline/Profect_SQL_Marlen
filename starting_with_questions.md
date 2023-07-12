Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

TOP 5 cities with highest transaction revenues
```SQL 
SELECT city, country, SUM(totaltransactionrevenue) as transaction_revenue
FROM all_sessions
WHERE transactions IS NOT NULL
GROUP BY city, country
ORDER BY transaction_revenue DESC
LIMIT 6
```

TOP countries with highest transaction revenues
```SQL
SELECT country, SUM(totaltransactionrevenue) as transaction_revenue
FROM all_sessions
WHERE transactions IS NOT NULL
GROUP BY country
ORDER BY transaction_revenue DESC
```

Answer:

Top 5 cities: 
	1) "Unknown city from USA", USA
	2) San Francisco, USA
	3) Sunnyvale, USA
	4) Atlanta, USA
	5) Palo Alto, USA
	6) Tel Aviv-Yafo, Israel (could be considered as #5, since 1st city is unknown)
	
Top countries:
	1) USA
	2) Israel
	3) Australia



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
In each city:
```SQL
SELECT alls.city, AVG(a.units_sold) AS AVG_products_ordered
FROM analytics a
JOIN all_sessions alls
ON a.visitid = CAST(alls.visitid AS NUMERIC)
WHERE a.units_sold IS NOT NULL
GROUP BY city
ORDER BY AVG_products_ordered DESC
```

In each country:
```SQL
SELECT alls.country, AVG(a.units_sold) AS AVG_products_ordered
FROM analytics a
JOIN all_sessions alls
ON a.visitid = CAST(alls.visitid AS NUMERIC)
WHERE a.units_sold IS NOT NULL
GROUP BY country
ORDER BY AVG_products_ordered DESC
```

Answer:

Average number of products sold (ordered) in each city:

"Chicago"	5.0
"Pittsburgh"	4.0
"New York"	3.820
"Houston"	2.50
"Toronto"	2.3333
"Sunnyvale"	2.2561
"not available in demo dataset"	1.6667
"Mountain View"	1.5576
"Hyderabad"	1.50
"Seattle"	1.4444
"San Francisco"	1.3636
"Kirkland"	1.0
"London"	1.0
"Munich"	1.0
"Palo Alto"	1.0
"Paris"	1.00000
"San Bruno"	1.0
"San Jose"	1.0
"Santiago"	1.0
"Singapore"	1.0
"Tel Aviv-Yafo"	1.
"Washington"	1.
"(not set)"	1.0
"Zurich"	1.0
"Ann Arbor"	1.0
"Atlanta"	1.0
"Austin"	1.0
"Bangkok"	1.0
"Berlin"	1.0
"Bogota"	1.0
"Dallas"	1.0
"Detroit"	1.0
"Dublin"	1.0
"Hong Kong"	1.0

Average number of products sold (ordered) in each country:

"Canada"	2.44
"United States"	2.167
"Bulgaria"	1.5
"Mexico"	1.5
"Japan"	1.4
"India"	1.167
"Egypt"	1.0
"France"	1.0
"Germany"	1.0
"Hong Kong"	1.0
"Indonesia"	1.0
"Ireland"	1.0
"Israel"	1.0
"Maldives"	1.0
"Netherlands"	1.0
"Romania"	1.0
"Singapore"	1.0
"South Korea"	1.0
"Sweden"	1.0
"Switzerland"	1.0
"Taiwan"	1.0
"Thailand"	1.0
"United Kingdom"	1.0
"Australia"	1.00
"Vietnam"	1.00
"Austria"	1.00
"Belgium"	1.00
"Chile"	1.00
"Colombia"	1.00
"Denmark"	1.00



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
For countries:
```SQL
SELECT als.v2productcategory, als.country, SUM(a.units_sold),
	RANK() OVER (
		PARTITION BY als.country 
		ORDER BY SUM(a.units_sold) DESC
	) units_sold_rank
FROM all_sessions als
JOIN analytics a
ON CAST(als.visitid AS NUMERIC) = a.visitid
WHERE a.units_sold IS NOT NULL AND a.units_sold >1 AND als.v2productcategory != '(not set)'
GROUP BY als.v2productcategory, als.country
```

For cities:
```SQL
SELECT als.v2productname, als.city, SUM(a.units_sold),
	RANK() OVER (
		PARTITION BY als.city 
		ORDER BY SUM(a.units_sold) DESC
	) units_sold_rank
FROM all_sessions als
JOIN analytics a
ON CAST(als.visitid AS NUMERIC) = a.visitid
WHERE a.units_sold IS NOT NULL AND a.units_sold >1 AND als.v2productname != '(not set)' AND city != 'not available in demo dataset'
GROUP BY als.v2productname, als.city
```

Answer:
For each country that sold more than 2 products there is a pattern, all of the products "home" products, Bulgaria's and Canada's top seller is Apparel, 
India's top seller Home Accessories, for USA is Bags/Backpacks with more than 150 ordered

If we look down for cities, then Google Alpine Style Backpack made 60 and 160 orders in Chicago and New York accordingly.
In the Sunnyvale leader is SPF product, because it is a sunny place to live in.



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```SQL
WITH top_selling_product AS (
	SELECT als.v2productname, als.country, SUM(a.units_sold) as total_sold,
		RANK() OVER (
		PARTITION BY als.country 
		ORDER BY SUM(a.units_sold) DESC
		) units_sold_rank
	FROM all_sessions als
	JOIN analytics a
	ON CAST(als.visitid AS NUMERIC) = a.visitid
	WHERE a.units_sold IS NOT NULL 
		AND als.v2productname != '(not set)' 
	GROUP BY als.v2productname, als.country
	ORDER BY als.country
)

SELECT v2productname, country
FROM top_selling_product
WHERE units_sold_rank = 1
```


Answer:
Top selling products in each country

"v2productname"							"country"
"Android Sticker Sheet Ultra Removable"	"Australia"
"Google Car Clip Phone Holder"			"Austria"
"Google 17 oz Double Wall Stainless Steel Insulated Bottle"	"Belgium"
"YouTube Men's Vintage Tank"			"Bulgaria"
"Waze Pack of 9 Decal Set"				"Canada"
"Sport Bag"								"Chile"
"YouTube Men's 3/4 Sleeve Henley"		"Colombia"
"26 oz Double Wall Insulated Bottle"	"Denmark"
"Android RFID Journal"					"Egypt"
"Android Wool Heather Cap Heather/Black"	"France"
"Android Lunch Kit"						"France"
"Google Doodle Decal"					"Germany"
"Google Twill Cap"						"Germany"
"Nest® Learning Thermostat 3rd Gen-USA - White"	"Hong Kong"
"Keyboard DOT Sticker"					"India"
"Google Rucksack"						"Indonesia"
"Google Laptop Backpack"				"Ireland"
"YouTube RFID Journal"					"Ireland"
"YouTube Hard Cover Journal"			"Israel"
"Google Lunch Bag"						"Japan"
"Google Men's Performance Full Zip Jacket Black"	"Maldives"
"Nest® Cam Indoor Security Camera - USA"	"Mexico"
"Android Men's Vintage Tank"			"Netherlands"
"8 pc Android Sticker Sheet"			"Romania"
"Google Men's Short Sleeve Performance Badge Tee Navy"	"Singapore"
"Galaxy Screen Cleaning Cloth"			"South Korea"
"Google Women's Short Sleeve Hero Tee Sky Blue"	"Sweden"
"YouTube Men's 3/4 Sleeve Henley"		"Switzerland"
"Google Men's Performance 1/4 Zip Pullover Heather/Black"	"Taiwan"
"Google Women's Short Sleeve Hero Tee Black"	"Taiwan"
"26 oz Double Wall Insulated Bottle"	"Thailand"
"Android Hard Cover Journal"			"United Kingdom"
"Google Alpine Style Backpack"			"United States"
"YouTube RFID Journal"					"Vietnam"




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
impact of revenue for each city:
```SQL
WITH revenue_per_city AS (
	SELECT als.city, SUM (revenue)/1000000 AS total_revenue
	FROM analytics a
	JOIN all_sessions als
	ON a.fullvisitorid = CAST(als.fullvisitorid AS NUMERIC)
	WHERE revenue IS NOT NULL AND city != 'not available in demo dataset'
	GROUP BY als.city
),
	revenue_sum AS (
	SELECT SUM(revenue_per_city.total_revenue) AS total_revenue_sum
	FROM revenue_per_city
)

SELECT city, ROUND(revenue_per_city.total_revenue/total_revenue_sum*100, 2) AS percent_of_total_revenue
FROM revenue_per_city
CROSS JOIN revenue_sum
GROUP BY city, percent_of_total_revenue
ORDER BY percent_of_total_revenue DESC
```

Answer:

"city"					"percent_of_total_revenue"
"Mountain View"			32.22
"San Bruno"				12.92
"New York"				10.65
"Sunnyvale"				9.28
"Chicago"				4.23
"Charlotte"				3.55
"Kirkland"				3.37
"San Francisco"			3.02
"Los Angeles"			2.88
"Jersey City"			2.85
"Austin"				2.63
"Seattle"				2.43
"Palo Alto"				2.17
"Milpitas"				1.51
"Toronto"				1.49
"Ann Arbor"				1.35
"Salem"					1.06
"San Jose"				0.73
"Cambridge"				0.51
"Fremont"				0.38
"Atlanta"				0.25
"South San Francisco"	0.25
"Denver"				0.13
"Yokohama"				0.09
"Zurich"				0.05
