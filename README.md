-- Superstore-Analysis
This project focuses on analyzing sales data from a superstore and the analysis is done on SQL. Exploring insights into sales trends, customer behavior and product performance.


-- DATA PRE_PROCESSING
-- Showing the number of unique customers that placed orders
SELECT
	COUNT(DISTINCT `Customer ID`) AS customers_count
FROM customers_table;

-- EXPLORATORY DATA ANALYSIS
-- 1. Showing performance by Region and Segment , sort to show region with highest order by Segment first
 SELECT  Region, Segment,
	COUNT(c.`Order ID`) AS Order_count,
	ROUND(sum(Sales),2) AS total_sales,
	ROUND(avg(Sales),2) AS avg_sales
 FROM customers_table AS c
 JOIN sales_table AS s
 ON c.`Order ID` = s.`Order ID`
 GROUP BY  Region, Segment
 ORDER BY Region DESC, Segment;

 
 -- 2. Showing Percentage Distribution among Cities
SELECT City, 
	ROUND(SUM(Sales),0) AS CitySales, 
	ROUND((SUM(Sales) * 100.0 / SUM(SUM(Sales)) OVER()),2) AS Percentage
FROM customers_table AS c
JOIN sales_table AS s
ON c.`Order ID` = s.`Order ID`
GROUP BY City
ORDER BY Percentage DESC
LIMIT 10;


-- 3. Showing percentage of profit margin (WHERE Profit_margin >= 30 is Very Good, Profit_margin >= 20 is Good,  Profit_margin < 20 is  Fair, Profit_margin < 0 is  Very Bad)
-- Show only Oder ID, Sales, Profit and Profit_margin with percentage of profit margin status
WITH enriched_sales_table AS (
SELECT *,
	ROUND((100 * Profit / Sales),2) AS Profit_margin
   FROM sales_table
    )
SELECT `Order ID`, Sales, Profit, Profit_margin,
CASE 
	WHEN Profit_margin >= 30 THEN 'Very Good'
	WHEN Profit_margin >= 20 THEN 'Good'
	WHEN Profit_margin >0 THEN 'Fair'
ELSE 'Bad'
END AS Profit_margin_status
FROM enriched_sales_table
LIMIT 10;


-- 4.  Showing Products Performance characteristics (count of orders, distinct customers, total sales and average quantity) in West region where number of products sold > 5 and Sales >= 5000
SELECT p.`Product ID`, 
	COUNT(c.`Order ID`) AS order_count,
	COUNT(DISTINCT `Customer ID`) AS customer_count,
    ROUND(SUM(Sales),0) AS total_sales,
	ROUND(AVG(Quantity),2) AS avg_quantity
FROM customers_table AS c
JOIN sales_table AS s
USING  (`Order ID`)
JOIN product_table AS p
ON s.`Product ID` = p.`Product ID`
WHERE Region = "West"
GROUP BY `Product ID`
HAVING order_count > 5 AND SUM(Sales) >=5000
LIMIT 10;


-- 5. Showing if discounts drive higher sales and profit where Discount = 0, No Discount, Discount is between 0 and 0.10, 1-10%, Discount is between 0.11 and 0.20, 11-20% and Discount >20&, Above 20%
SELECT
    CASE
        WHEN Discount = 0 THEN 'No Discount'
        WHEN Discount BETWEEN 0.01 AND 0.10 THEN '1-10%'
        WHEN Discount BETWEEN 0.11 AND 0.20 THEN '11-20%'
        ELSE 'Above 20%'
    END AS Discount_Range,
    ROUND(SUM(Sales),0) AS Total_Sales,
    ROUND(SUM(Profit),0) AS Total_Profit,
    ROUND(AVG(Profit),2) AS Avg_Profit
FROM sales_table
GROUP BY
    CASE
        WHEN Discount = 0 THEN 'No Discount'
        WHEN Discount BETWEEN 0.01 AND 0.10 THEN '1-10%'
        WHEN Discount BETWEEN 0.11 AND 0.20 THEN '11-20%'
        ELSE 'Above 20%'
    END
ORDER BY Total_Profit DESC;


-- 6. Showing which mode of shipping generated highest profit and sales
SELECT  `Ship Mode`, 
    ROUND(SUM(Sales),0) AS total_sales, 
    ROUND(SUM(Profit),0) AS total_profit
FROM orders_table AS o
JOIN Sales_table as s
USING (`Order ID`)
GROUP BY  `Ship Mode`
ORDER BY  total_profit DESC, total_sales DESC;


-- 7. Showing geographic trends among Regions using Sales as a metric
SELECT Region, 
	ROUND(SUM(Sales),2) AS total_sales
FROM customers_table AS c
JOIN sales_table AS s
USING (`Order ID`)
GROUP BY Region
ORDER BY total_sales DESC;


-- 8. Showing orders with sales above 500 in California
SELECT c.`order id`, sales
FROM customers_table AS c
JOIN sales_table AS s
ON c.`Order ID` = s.`Order ID`
WHERE sales >500 AND state = "California"
LIMIT 10;


-- 9. Showing Orders with sales between 100 and 500 
SELECT `order ID`, sales
FROM sales_table
WHERE sales BETWEEN 100 AND 500
LIMIT 10;


-- 10. Showing Sales outside of the East Region
SELECT Region, 
	ROUND(SUM(Sales),0) AS total_sales
FROM customers_table AS c
JOIN sales_table AS s
ON c.`Order ID` = s.`Order ID`
WHERE Region <> 'East'
GROUP BY Region;


-- 11. Showing top 10 customers and their region
SELECT `Customer ID`, Region, sales
FROM customers_table AS c
JOIN sales_table AS s
ON c.`Order ID` = s.`Order ID`
ORDER BY Sales DESC
LIMIT 10;


-- 12. Showing 5 top Products with highest profit only in central region
SELECT `Product ID`, profit, region
FROM customers_table AS c
JOIN sales_table AS s
USING (`Order ID`)
WHERE region = 'Central'
ORDER BY profit DESC
LIMIT 5; 

 
  -- 13. Showing states that are most represented
SELECT State, 
	COUNT(*) AS total_records
FROM customers_table
GROUP BY State
ORDER BY total_records DESC
LIMIT 10;


-- 14. Showing products where the total quantity sold is greater than 50
SELECT s.`Product ID`, 
	SUM(Quantity) AS total_quantity
FROM sales_table AS s
JOIN product_table AS p
ON s.`Product ID` = p.`Product ID`
GROUP BY s.`Product ID`
HAVING total_quantity > 50
LIMIT 10;

<img width="768" height="306" alt="image" src="https://github.com/user-attachments/assets/3a989110-8b67-4b8c-ad19-a2d64c52711f" />

USE superstore_db;

SELECT * FROM customers_table;

SELECT * FROM orders_table;

SELECT * FROM product_table;

SELECT * FROM sales_table;
