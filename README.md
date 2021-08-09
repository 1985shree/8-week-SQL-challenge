# 8-week-SQL-challenge
This lists the fun problems and solutions of [Danny Ma's 8 week SQL challenge!](https://8weeksqlchallenge.com/getting-started/)

First week:
[Danny's Diner](https://8weeksqlchallenge.com/case-study-1/)


Question1. What is the total amount each customer spent at the restaurant?

SQL query to find the answer:

``` SQL

SELECT
  	dannys_diner.sales.customer_id,
    SUM(price) AS total_amount
	FROM dannys_diner.sales
	INNER JOIN dannys_diner.menu
	ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
GROUP BY dannys_diner.sales.customer_id
ORDER BY dannys_diner.sales.customer_id;

```


Question2. How many days has each customer visited the restaurant?

SQL query to find the answer:

```SQL
SELECT
	dannys_diner.sales.customer_id, 
    COUNT(order_date) as count
    FROM dannys_diner.sales
 GROUP BY dannys_diner.sales.customer_id
 ORDER BY dannys_diner.sales.customer_id;

```

Question3. What was the first item from the menu purchased by each customer?

SQL query to find the answer:

```SQL
SELECT
  	dannys_diner.menu.product_name,
    dannys_diner.sales.customer_id,
    dannys_diner.sales.order_date
	FROM dannys_diner.sales
	INNER JOIN dannys_diner.menu
	ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
GROUP BY dannys_diner.menu.product_name, dannys_diner.sales.customer_id, dannys_diner.sales.order_date
ORDER BY dannys_diner.sales.order_date
LIMIT 3;

```

Question4. What is the most purchased item on the menu and how many times was it purchased by all customers?

SQL query to find the answer

```SQL

SELECT
  	COUNT(dannys_diner.sales.product_id)as Times_ordered,
    dannys_diner.menu.product_name
    FROM dannys_diner.sales
	INNER JOIN dannys_diner.menu
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
GROUP BY dannys_diner.sales.product_id, dannys_diner.menu.product_name
ORDER BY Times_ordered DESC
LIMIT 1;

```

Question 5. 5. Which item was the most popular for each customer?

SQL query to find the answer:

```SQL

SELECT
  	COUNT(dannys_diner.sales.product_id)as Times_ordered,
    dannys_diner.menu.product_name,
    dannys_diner.sales.customer_id as Customer
    FROM dannys_diner.sales
	INNER JOIN dannys_diner.menu
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
GROUP BY Customer, dannys_diner.sales.product_id, dannys_diner.menu.product_name 
ORDER BY Times_ordered DESC;

```

[Try the codes here to check the answers!](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
