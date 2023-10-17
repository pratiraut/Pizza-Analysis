## Introduction

This Data Analysis project leverages the data to enable the business to better understand themselves and their customers, leading to higher profits and better performances. By analyzing Key Metrics that the business use to measure performance, writing SQL queries to calculate these metrics, and producing report-ready results.

Dataset [Downlode here]

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
 I have data in the form of Spreadsheets but we have some limitations, Spreadsheets were originally designed for one user, and their characteristics reflect that. They’re great for a single user or a small number of users who don’t need to do a lot of incredibly complicated data manipulation. Databases, on the other hand, are designed to hold much larger collections of organized information—massive amounts, sometimes. Databases allow multiple users at the same time to quickly and securely access and query the data using highly complex logic and language That is why transferring  the data in the database.
 
Data within the most common types of databases in operation today is typically modeled in rows and columns in a series of tables to make processing and data querying efficient. The data can then be easily accessed, managed, modified, updated, controlled, and organized. Most databases use structured query language (SQL) for writing and querying data.

 
- Creating A table name called pizza_sales. 

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

- Import data into the table pizza_sales.

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


- Understanding the data in the table

```sql
SELECT * FROM pizza_sales;
```

### KPI'S Finding


Key Performance Indicators - A Metric with some values that a company uses to measure its performance.

A Key Performance Indicator (KPI) is a measurable target that indicates how individuals or businesses are performing in terms of meeting their goals. Reviewing and evaluating KPIs helps organizations determine whether or not they are on track for hitting their desired objectives.

By looking at several key indicators, which may include categories such as revenue, and orders, businesses can identify successes, as well as what is not working. Analyzing KPIs on a regular basis provides a solid overview of how well a business is performing, which allows the folks in charge to decide if current operations should be continued, or if a change of strategy is needed.


1. Total Revenue
2. Average Order Values
3. Total Pizza Sold
4. Total Orders
5. Average Pizza per Orders   

Total Revenue - Total Revenue is the sum of the total price of all pizza orders.

```sql
SELECT SUM(total_price) AS total_revenue
FROM pizza_sales;
```

Output -

![Total_revenue](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/89def362-9405-44ea-982c-7bad7c970b76)


Average Order Values - Average Order Value is the average amount spent per order.

```sql
SELECT SUM(total_price)/ COUNT(DISTINCT order_id) AS avg_order_values
FROM pizza_sales;
```

Output -

![avgorva](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/97035db7-c25e-48e9-a3d1-71a32433620b)


Total Pizza Sold - Total Pizzas Sold is the sum of the quantities of all pizzas sold.

```sql
SELECT SUM(quantity) AS total_pizza_sold
FROM pizza_sales;
```

Output -

![pizzasold](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/6b595fe0-d2a6-467e-9f56-d7210f84c3c3)


Total Orders - Total Orders is the total number of orders placed.

```sql
SELECT COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales;
```

Output -

![total orders](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/7fe4867a-3907-493c-b561-d32b54f0ef7a)


Average Pizza per Order.

```sql
SELECT CAST(CAST(SUM(quantity)AS NUMERIC(10,2))
        /CAST(COUNT(DISTINCT order_id)AS NUMERIC (10,0))AS NUMERIC (10,2))
FROM pizza_sales;
```

Output -

![avg pizza per order](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/226fd3e2-f2eb-4166-9cf6-4a65462e9ae2)


### SQL queries

Writing some SQL queries to find out daily trends, monthly trends, percentages of sales, and Top 5 best sellers by Revenue.

1. Daily Trends for total orders.

```sql
SELECT EXTRACT(dow FROM order_date) AS order_day,
       COUNT(DISTINCT order_id) AS total_orders
FROM pizza_sales
GROUP BY order_day;
```

Output - 

![daily trends](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/79d11fc1-801b-4d5f-9851-1bf6dfcd6f46) 

In the given output table we can observe on which day of week the maximum orders placed.

2. Monthly Trends for total orders.

```sql
SELECT EXTRACT(month FROM order_date) AS month_orders,
       COUNT(DISTINCT order_id)AS total_orders
FROM pizza_sales
GROUP BY month_orders
ORDER BY total_orders DESC;
```

Output -

![monthly trends](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/57749dd6-80a8-45f1-868c-3e7c243f279a)


3. Percentage of Sales by pizza category.

```sql
SELECT pizza_category,
       SUM(total_price) AS total_sales,
       CAST(SUM(total_price) * 100/(SELECT SUM(total_price)FROM pizza_sales) AS NUMERIC(10,2)) AS per_total_sales
FROM pizza_sales
GROUP BY pizza_category;
```

Output-

![category pizza](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/02acd471-7fea-423e-ae7d-31fbb255a3a5)


4. Percentage of sales by pizza size.

```sql
SELECT pizza_size,
       CAST(SUM(total_price) AS decimal(10,2))AS total_sales,
	   CAST(SUM(total_price)*100/(SELECT SUM(total_price) FROM pizza_sales) AS decimal (10,2)) AS per_total_sales 
 FROM pizza_sales
GROUP BY pizza_size
ORDER BY per_total_sales DESC;
```

Output-

![pizza size](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/a3722a46-a652-44d2-849d-cba1c4764931)


5. TOP 5 Best seller by Revenue.

 ```sql
SELECT pizza_name,
	   SUM(total_price) AS total_revenue
  FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue DESC
LIMIT 5;
```

Output -

![Top 5](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/2fb97bd6-65f0-4738-801c-2e3b0504e42b)


6. BOTTOM 5 Seller by Revenue.

```sql
SELECT pizza_name,
	   SUM(total_price) AS total_revenue
  FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue 
LIMIT 5;
```

Output - 

![Bottom 5](https://github.com/pratiraut/Pizza-Analysis/assets/146583441/036c752e-33e6-4a6b-b5eb-85542fe04ac3)







### Dashboard 

[downlode here]
