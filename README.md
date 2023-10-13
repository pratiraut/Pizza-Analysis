## Introduction

This Data Analysis project leverages the data to enable businesses to better understand themselves and their customers, leading to higher profits and better performances. By analyzing Key Metrics that businesses use to measure performance, writing SQL queries to calculate these metrics, and producing report-ready results.

## Problem statement 

- KPI's
- Daily Trends
- Monthly Trends
- Percentage of Sales
- TOP 5 best seller
- Unique Users
  
## Tools
- PostgreSQL
- Power BI

## Process
1. Getting a date in Postgresql
2. KPI finding
3. SQL queries
4. Power Query
5. Vizavalization

### Getting a Data in Postgresql

Creating A table name called pizza_sales. 

```sql
CREATE TABLE IF NOT EXISTS pizza_sales
(
    pizza_id    INT,
    order_id    INT,
    pizza_name_id VARCHAR,
    quantity    INT,
    order_date  DATE,
    order_time  TIME,
    unit_price  NUMERIC,
    total_price NUMERIC,
    pizza_size  VARCHAR,
    pizza_category    VARCHAR,
    pizza_ingredients VARCHAR,
    pizza_name        VARCHAR
);
```

Import data into the table pizza_sales.

```sql
COPY pizza_sales( 
	pizza_id,
    order_id,
    pizza_name_id,
    quantity,
    order_date,
    order_time,
    unit_price,
    total_price,
    pizza_size,
    pizza_category,
    pizza_ingredients,
    pizza_name
)
FROM 'F:\pizza_sales.csv'
DELIMITER ','
CSV HEADER;
```


Understanding the data in the table

```sql
SELECT * FROM pizza_sales;
```

### KPI'S Finding

Key Performance Indicators - A Metric with some values that a company uses to measure its performances

1. Total Revenue
2. Average Order Values
3. Total Pizza Sold
4. Total Orders
5. Average Pizza per Orders   

Total Revenue - Total Revenue is the sum of the total price of all pizza orders

```sql
SELECT SUM(total_price) AS total_revenue
FROM pizza_sales;
```

Average Order Values - Average Order Value is the average amount spent per order

```sql
SELECT SUM(total_price)/ COUNT(DISTINCT order_id) AS avg_order_values
FROM pizza_sales;
```

Total Pizza Sold - Total Pizzas Sold is the sum of the quantities of all pizzas sold

```sql
SELECT SUM(quantity) AS total_pizza_sold
FROM pizza_sales;
```

Total Orders - Total Orders is the total number of orders placed

```sql
SELECT COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales;
```

Average Pizza per Orders

```sql
SELECT CAST(CAST(SUM(quantity)AS NUMERIC(10,2))
        /CAST(COUNT(DISTINCT order_id)AS NUMERIC (10,0))AS NUMERIC (10,2))
FROM pizza_sales;
```

### SQL queries

Writing some SQL queries to find out daily trends, monthly trends, percentages of sales, and Top 5 best sellers by Revenue.

1. Daily Trends for total orders.

```sql
SELECT EXTRACT(dow FROM order_date) AS order_day,
       COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales
GROUP BY order_day;
```

2. Monthly Trends for total orders.

```sql
SELECT EXTRACT(month FROM order_date) AS month_orders,
       COUNT(DISTINCT order_id)AS total_orders
FROM pizza_sales
GROUP BY month_orders
ORDER BY total_orders DESC;
```

3. Percentage of Sales by pizza category.

```sql
SELECT pizza_category,
       SUM(total_price) AS total_sales,
       CAST(SUM(total_price) * 100/(SELECT SUM(total_price)FROM pizza_sales) AS NUMERIC(10,2)) AS per_total_sales
FROM pizza_sales
GROUP BY pizza_category;
```

4. Percentage of sales by pizza size.

```sql
SELECT pizza_size,
       CAST(SUM(total_price) AS decimal(10,2))AS total_sales,
	   CAST(SUM(total_price)*100/(SELECT SUM(total_price) FROM pizza_sales) AS decimal (10,2)) AS per_total_sales 
 FROM pizza_sales
GROUP BY pizza_size
ORDER BY per_total_sales DESC;
```

5. TOP 5 Best seller by Revenue.

 ```sql
SELECT pizza_name,
	   SUM(total_price) AS total_revenue
  FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue DESC
LIMIT 5;
```

6. BOTTOM 5 Seller by Revenue.

```sql
SELECT pizza_name,
	   SUM(total_price) AS total_revenue
  FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue 
LIMIT 5;
```

