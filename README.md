# Coffee_Shop_Sales_Project
This project focuses on analyzing Key Performance Indicators (KPIs) related to the sales of a coffee shop, using SQL to perform various calculations and derive insights.

## Problem Statement

We aim to analyze the KPIs related to sales, orders, and quantity sold for each month, calculate month-on-month (MoM) changes, and derive insights that help inform business decisions. The following KPIs will be calculated:

### Total Sales Analysis:

    -Calculate total sales for each month.
    -Determine MoM increase or decrease in sales.
    -Calculate the difference in sales between the selected month and the previous month.

### Total Orders Analysis:

    -Calculate total orders for each month.
    -Determine MoM increase or decrease in the number of orders.
    -Calculate the difference in the number of orders between the selected month and the previous month.

### Total Quantity Sold Analysis:

    Calculate the total quantity sold for each month.
    Determine MoM increase or decrease in the total quantity sold.
    Calculate the difference in the total quantity sold between the selected month and the previous month.

## Project Steps

  **Data Walkthrough**: Review the dataset for potential inconsistencies and errors.
  **Raw Data File Preparation**: Ensure the dataset is ready for database import.
  **Creating Database**: Set up the database and import the dataset.
  **Cleaning Imported File**: Clean the dataset using SQL queries.
  **Changing Data Types**: Modify data types for accurate calculations.
  **Firing SQL Queries**: Write and execute SQL queries to calculate KPIs.
  **Storing Results**: Store the results for further analysis.
  **Preparing SQL Documents**: Organize and save the queries and results for documentation purposes.

## SQL Queries

### Data Cleaning and Preparation

   ##### Convert Date to Proper Format:

```sql
UPDATE coffee_shop_sales
SET transaction_date = STR_TO_DATE(transaction_date, '%d-%m-%Y');
```

Convert Time to Proper Format:

sql

UPDATE coffee_shop_sales
SET transaction_time = STR_TO_DATE(transaction_time, '%H:%i:%s');

Alter Date and Time Data Types:

sql

ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_date DATE;

ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_time TIME;

Rename Column for Better Readability:

sql

    ALTER TABLE coffee_shop_sales
    CHANGE COLUMN `ï»¿transaction_id` transaction_id INT;

KPI Calculation Queries

    Total Sales for May:

    sql

SELECT ROUND(SUM(unit_price * transaction_qty)) AS Total_Sales 
FROM coffee_shop_sales 
WHERE MONTH(transaction_date) = 5;

Total Sales KPI - MoM Difference and Growth:

sql

SELECT 
    MONTH(transaction_date) AS month,
    ROUND(SUM(unit_price * transaction_qty)) AS total_sales,
    (SUM(unit_price * transaction_qty) - LAG(SUM(unit_price * transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(unit_price * transaction_qty), 1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) IN (4, 5)
GROUP BY 
    MONTH(transaction_date)
ORDER BY 
    MONTH(transaction_date);

Result:

    April: Total Sales: 118,941
    May: Total Sales: 156,728, MoM Increase: 31.76%

Total Orders KPI - MoM Difference and Growth:

sql

SELECT 
    MONTH(transaction_date) AS month,
    ROUND(COUNT(transaction_id)) AS total_orders,
    (COUNT(transaction_id) - LAG(COUNT(transaction_id), 1) 
    OVER (ORDER BY MONTH(transaction_date))) / LAG(COUNT(transaction_id), 1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) IN (4, 5)
GROUP BY 
    MONTH(transaction_date)
ORDER BY 
    MONTH(transaction_date);

Result:

    April: Total Orders: 25335
    May: Total Orders: 333527, MoM Increase: 32.33%

Daily Sales, Quantity, and Total Orders for May 18:

sql

    SELECT
        SUM(unit_price * transaction_qty) AS total_sales,
        SUM(transaction_qty) AS total_quantity_sold,
        COUNT(transaction_id) AS total_orders
    FROM 
        coffee_shop_sales
    WHERE 
        transaction_date = '2023-05-18';

    Result:
        May 18: Total Sales: 5,593.47, Total Quantity Sold: 1,659, Total Orders: 1,192

Additional Insights

    Sales Trend Over May:

    sql

SELECT AVG(total_sales) AS average_sales
FROM (
    SELECT 
        SUM(unit_price * transaction_qty) AS total_sales
    FROM 
        coffee_shop_sales
    WHERE 
        MONTH(transaction_date) = 5
    GROUP BY 
        transaction_date
) AS internal_query;

Result: Average Sales for May: 5,055.73

Comparing Daily Sales with Average Sales:

sql

    SELECT 
        day_of_month,
        CASE 
            WHEN total_sales > avg_sales THEN 'Above Average'
            WHEN total_sales < avg_sales THEN 'Below Average'
            ELSE 'Average'
        END AS sales_status,
        total_sales
    FROM (
        SELECT 
            DAY(transaction_date) AS day_of_month,
            SUM(unit_price * transaction_qty) AS total_sales,
            AVG(SUM(unit_price * transaction_qty)) OVER () AS avg_sales
        FROM 
            coffee_shop_sales
        WHERE 
            MONTH(transaction_date) = 5
        GROUP BY 
            DAY(transaction_date)
    ) AS sales_data
    ORDER BY 
        day_of_month;

    Results: Identified which days had above-average and below-average sales.

Insights and Learnings

    Sales Growth: We observed a significant MoM increase in sales between April and May, indicating effective promotional campaigns or other business factors.
    Order Growth: Similar growth trends were identified in the number of orders, suggesting increased customer activity.
    Daily Sales Patterns: Sales tend to fluctuate on a daily basis, with certain days performing better than others.
    Data Preparation: Data cleaning, particularly adjusting date and time formats, is crucial for accurate calculations.
    Business Decisions: These insights can help the coffee shop make informed decisions about inventory, staffing, and marketing based on high-traffic days.

Recommendations

    Marketing: Continue leveraging promotions during high-performing months to sustain sales growth.
    Inventory Management: Focus inventory planning around days with higher-than-average sales to avoid stockouts.
    Order Processing: Monitor order volume to ensure sufficient staff and resources during peak sales periods.
    Further Analysis: Conduct more detailed analyses on customer preferences and seasonal trends to maximize profits.

What I Learned

    SQL Proficiency: This project helped enhance my SQL skills, particularly in window functions, date manipulation, and data aggregation.
    Data Cleaning: I learned the importance of cleaning and preprocessing data for accurate analysis.
    Business Understanding: Understanding sales KPIs in a real-world context gave me insights into how businesses can track and optimize performance over time.

Conclusion

This project demonstrates how SQL can be used to analyze sales KPIs and derive actionable insights for businesses. With proper data cleaning, transformation, and analysis, companies can make data-driven decisions to improve performance and profitability.

```
