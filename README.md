# Coffee_Shop_Sales_Project
This project focuses on analyzing Key Performance Indicators (KPIs) related to the sales of a coffee shop, using SQL to perform various calculations and derive insights.

## Introduction

The primary objective of this analysis is to gain insights into sales performance over a given period. Using Power BI, we visualized various key metrics to identify trends, patterns, and opportunities for improvement. The dataset consists of sales transactions from a coffee shop, and this report outlines the analysis of total sales, orders, and quantities sold, along with detailed breakdowns by day, week, store location, and product categories. SQL was used to preprocess and query the data, ensuring efficient data extraction and transformation for analysis in Power BI.

## Background

This analysis aims to explore key performance indicators (KPIs) related to total sales, orders, and quantities sold. To address the business challenges, several Power BI visualizations were used to assist in decision-making, including heat maps, line charts, and bar charts. The key areas of focus include total monthly sales, sales trends by location and product, and the identification of top-performing products and store locations. SQL queries were utilized not only for data retrieval and preprocessing but also for KPI calculation and analysis, ensuring accurate and detailed insights for the business.

## Project Steps

  **Data Walkthrough**: Review the dataset for potential inconsistencies and errors.
  
  **Raw Data File Preparation**: Ensure the dataset is ready for database import.
  
  **Creating Database**: Set up the database and import the dataset.
  
  **Cleaning Imported File**: Clean the dataset using SQL queries.
  
  **Changing Data Types**: Modify data types for accurate calculations.
  
  **Firing SQL Queries**: Write and execute SQL queries to calculate KPIs.
  
  **Storing Results**: Store the results for further analysis.
  
  **Preparing SQL Documents**: Organize and save the queries and results for documentation purposes.


## Analysis

### 1. Data Cleaning and Preparation

   #### Convert Date to Proper Format:

```sql
UPDATE coffee_shop_sales
SET transaction_date = STR_TO_DATE(transaction_date, '%d-%m-%Y');
```

   #### Convert Time to Proper Format:

```sql
UPDATE coffee_shop_sales
SET transaction_time = STR_TO_DATE(transaction_time, '%H:%i:%s');

Alter Date and Time Data Types:

ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_date DATE;

ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_time TIME;
```
### 2. Total Sales Analysis

**Objective**: Calculate and compare monthly sales, providing insights into trends and growth. SQL queries were used to calculate total sales for each month, with May 2023 being a focus.

#### Total Sales 

```sql
SELECT ROUND(SUM(unit_price * transaction_qty)) AS Total_Sales 
FROM coffee_shop_sales 
WHERE MONTH(transaction_date) = 5;
```
The total sales for May amounting to 156,728 $ indicates a solid revenue month for the coffee shop

**Insight**: Total sales for May provide an overview of the business's revenue during this period.

#### Total Sales KPI - MoM Difference and Growth:

This SQL query calculates the total sales for April and May and determines the month-over-month (MoM) percentage increase or decrease in sales. Here’s a breakdown of the query:

```sql
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
```
**Insight**: The query results indicate that the business experienced a 31.76% increase in sales from April to May. This significant increase suggests that the business strategies or promotions implemented during this period were effective

### 3.Total Orders KPI - MoM Difference and Growth:

This SQL query calculates the total number of orders for April and May and determines the month-over-month (MoM) percentage increase or decrease in the number of orders. Here’s a breakdown of the query:

```sql

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
```

**Insight**: The query results indicate that the business experienced a 32.33% increase in total orders from April to May. This significant increase suggests that the business strategies or promotions implemented during this period were effective
Daily Sales, Quantity, and Total Orders for May 18:

**Effective Promotions**: The increase in orders could be attributed to successful promotional campaigns or discounts offered in May.

**Seasonal Trends**: There might be a seasonal trend where orders naturally increase in May due to factors such as holidays or favorable weather.

**Improved Customer Engagement**: Enhanced customer engagement strategies, such as better customer service or targeted marketing, might have contributed to the increase in orders.

### 4.Calendar Heat Map for Sales

This SQL query calculates the total sales, total quantity sold, and total number of orders for a specific date, May 18, 2023. 

```sql
    SELECT
        SUM(unit_price * transaction_qty) AS total_sales,
        SUM(transaction_qty) AS total_quantity_sold,
        COUNT(transaction_id) AS total_orders
    FROM 
        coffee_shop_sales
    WHERE 
        transaction_date = '2023-05-18';
```
**Insight**: Total Sales: $5,593.47.This value represents the total revenue generated on May 18, 2023, indicating a strong sales day for the coffee shop.

Total Quantity Sold: 1,659 units.This shows the total number of items (e.g., coffee, snacks) sold, providing insight into the demand for products on that particular day.

Total Orders: 1,192 transactions.The number of individual orders placed on May 18, highlighting customer activity and transaction volume.

### 5.Sales by weekday/weekend:

The objective of this SQL query is to analyze and compare sales performance between weekdays and weekends for the month of May. 

```sql

SELECT 
    CASE 
        WHEN DAYOFWEEK(transaction_date) IN (1, 7) THEN 'Weekends'
        ELSE 'Weekdays'
    END AS day_type,
    ROUND(SUM(unit_price * transaction_qty),2) AS total_sales
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) = 5
GROUP BY 
    CASE 
        WHEN DAYOFWEEK(transaction_date) IN (1, 7) THEN 'Weekends'
        ELSE 'Weekdays'
    END;
```

**Insight** :Sales on weekdays significantly exceed those on weekends, indicating that the coffee shop experiences higher revenue on weekdays.The lower sales on weekends might suggest opportunities for promotions or marketing strategies to boost sales during this period.

#### 6.Comparing Daily Sales with Average Sales:

Calculate the total sales for each day in May and the overall average sales for May.

```sql
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

```
| Day of Month | Sales   | Status        | Total Sales |
|--------------|---------|---------------|-------------|
| 1            | 5418.00 | Above Average | 5418.00     |
| 2            | 4731.45 | Below Average | 4731.45     |
| 3            | 4625.50 | Below Average | 4625.50     |
| 4            | 4589.70 | Below Average | 4589.70     |
| 5            | 4701.00 | Below Average | 4701.00     |
| 6            | 4205.15 | Below Average | 4205.15     |
| 7            | 4542.70 | Below Average | 4542.70     |
| 8            | 5604.21 | Above Average | 5604.21     |
| 9            | 5100.97 | Above Average | 5100.97     |
| 10           | 5256.33 | Above Average | 5256.33     |
| 11           | 4850.06 | Below Average | 4850.06     |
| 12           | 4681.13 | Below Average | 4681.13     |
| 13           | 5511.53 | Above Average | 5511.53     |
| 14           | 5052.65 | Below Average | 5052.65     |
| 15           | 5384.98 | Above Average | 5384.98     |
| 16           | 5542.13 | Above Average | 5542.13     |
| 17           | 5657.88 | Above Average | 5657.88     |
| 18           | 5583.47 | Above Average | 5583.47     |
| 19           | 5657.88 | Above Average | 5657.88     |
| 20           | 5519.28 | Above Average | 5519.28     |
| 21           | 5370.81 | Above Average | 5370.81     |
| 22           | 5541.16 | Above Average | 5541.16     |
| 23           | 5242.91 | Above Average | 5242.91     |
| 24           | 5391.45 | Above Average | 5391.45     |
| 25           | 5230.85 | Above Average | 5230.85     |
| 26           | 5300.95 | Above Average | 5300.95     |
| 27           | 5559.15 | Above Average | 5559.15     |
| 28           | 4338.65 | Below Average | 4338.65     |
| 29           | 3959.50 | Below Average | 3959.50     |
| 30           | 4835.48 | Below Average | 4835.48     |
| 31           | 4684.13 | Below Average | 4684.13     |

 **Insight**:The sales data reveals a mix of high and low performing days, indicating variability in daily sales. Notable high sales days include the 8th, 13th, 15th, and 17th, with the 8th reaching $5,604.21, significantly above the average. Conversely, days like the 6th, 7th, 28th, and 29th had lower sales, with the 29th being the lowest at $3,959.50. The highest sales day was the 19th, with $5,657.88, suggesting that analyzing factors contributing to this peak can help replicate success. Categorizing days can reveal patterns, such as higher sales on weekends, indicating increased customer traffic. This data can be strategically used to plan promotions on low sales days and ensure adequate staffing and inventory on high sales days.

## Insights and Learnings

The coffee shop's sales data for May reveals a positive trend, with total sales reaching $156,728 and an average daily sales figure of $5,055.73. Notably, there was a significant month-over-month (MoM) increase in sales and orders from April to May, with sales rising by 31.76% and the number of orders increasing by 32.33%.

Daily sales varied considerably throughout the month, with the highest sales recorded on May 19 ($5,657.88) and the lowest on May 29 ($3,959.50). High-performing days such as May 8, 13, 15, and 17 suggest successful promotions or special events, while low-sales days may indicate areas for improvement.

Overall, May's data reflects strong revenue performance and highlights the effectiveness of recent business strategies. The variability in daily sales offers insights into customer behavior and operational efficiency, providing opportunities to refine marketing efforts and optimize inventory and staffing.

## Recommendations
    
**Analyze Successful Days**:Investigate factors contributing to high sales on days like May 8, 13, 15, 17, and 19.Identify any promotions, events, or special offers that drove these peaks.Replicate successful strategies on other days to boost sales.

**Boost Low-Sales Days**:Analyze causes behind lower sales on days such as May 6, 7, 28, and 29.Implement targeted strategies, such as special promotions or events, to increase sales on these days.

**Refine Promotional Strategies**:Continue monitoring and adjusting promotional efforts based on their performance.Experiment with new marketing campaigns and measure their impact on sales and customer engagement.

**Optimize Staffing and Inventory**:Adjust staffing levels and inventory management according to peak and low-sales days.Ensure adequate staffing during high-traffic periods and manage inventory to prevent stockouts or overstocking.

**Enhance Customer Engagement**:Offer loyalty programs, personalized marketing, or exclusive offers to boost repeat business. Increase customer engagement to drive more sales.

**Consider Seasonal Trends**: Plan for seasonal promotions or product offerings based on potential seasonal factors affecting sales.Capitalize on trends and customer preferences during different times of the year.

**Monitor and Adjust**: Continuously track sales and order data to identify trends.Regularly review performance metrics and adjust strategies to ensure sustained growth and profitability.

## What I Learned

**SQL Proficiency** : This project helped enhance my SQL skills, particularly in window functions, date manipulation, and data aggregation.

**Data Cleaning**: I learned the importance of cleaning and preprocessing data for accurate analysis.

**Business Understanding**: Understanding sales KPIs in a real-world context gave me insights into how businesses can track and optimize performance over time.

## Conclusion

The analysis of the coffee shop's sales data for May provides valuable insights into performance trends and customer behavior. The significant month-over-month increases in both sales and orders highlight the effectiveness of recent strategies and promotions. Daily sales data reveals variability, offering opportunities to enhance business operations.

By leveraging successful strategies from high-sales days and addressing the challenges on lower-sales days, the coffee shop can further optimize its performance. Recommendations include refining promotional efforts, adjusting staffing and inventory, and enhancing customer engagement. Monitoring and adapting to sales trends will ensure continued growth and improved profitability.

Overall, the insights derived from this analysis can guide strategic decisions and drive future success for the coffee shop.
```
