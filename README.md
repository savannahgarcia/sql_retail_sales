# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis   
**Database**: `p1_retail_db`

This project is a follow-along tutorial created by Zero Analyst. Designed to demonstrate my SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by downloading a sample database named `p1_retail_db`.
- **Table Creation**: Using MySQL import wizard, a table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.


### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Retrieve all columns for sales made on 2022-11-05**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Retrieve all transactions where the category is 'Clothing' and the quantity sold is equal to or more than 4 in the month of Nov-2022**:
```sql
SELECT *
FROM retail_sales
WHERE
	category = 'Clothing'
    AND
    quantity >= 4
    AND
    sale_date LIKE '2022-11-__';
```

3. **Total sales (total_sale) for each category**:
```sql
SELECT 
	category,
    SUM(total_sale) AS net_sale,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY 1;
```

4. **Average age of customers who purchased items from the 'Beauty' category**:
```sql
SELECT
	ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

5. **All transactions where the total_sale is greater than 1000**:
```sql
SELECT
	*
FROM retail_sales
WHERE total_sale > 1000;
```

6. **Total number of transactions (transactions_id) made by each gender in each category**:
```sql
SELECT
	category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP
	BY
    category,
    gender
ORDER BY 1;
```

7. **Average sale for each month, best selling month in each year**:
```sql
SELECT 
    year,
    month,
    avg_sale
FROM (
    SELECT 
        year,
        month,
        avg_sale,
        RANK() OVER(
            PARTITION BY year 
            ORDER BY avg_sale DESC
        ) as rnk
    FROM (
        SELECT 
            EXTRACT(YEAR FROM sale_date) as year,
            EXTRACT(MONTH FROM sale_date) as month,
            AVG(total_sale) as avg_sale
        FROM retail_sales
        GROUP BY 1, 2
    ) as grouped
) as ranked
WHERE rnk = 1;
```

8. **Top 5 customers based on the highest total sales**:
```sql
SELECT
	customer_id,
    SUM(total_sale)AS total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

9. **Number of unique customers who purchased items from each category**:
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

10. **Create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift;
```

## Findings

- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project was created by Zero Analyst and serves as an intro to covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.
