# 8-week-SQL-challenge
This lists the fun problems and solutions of [Danny Ma's 8 week SQL challenge!](https://8weeksqlchallenge.com/getting-started/)

First week:
[Danny's Diner](https://8weeksqlchallenge.com/case-study-1/)


Question1. What is the total amount each customer spent at the restaurant?

Answer SQL query:

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

Answer SQL query:

```SQL
SELECT
	dannys_diner.sales.customer_id, 
    COUNT(order_date) as count
    FROM dannys_diner.sales
 GROUP BY dannys_diner.sales.customer_id
 ORDER BY dannys_diner.sales.customer_id;

```

Question3. What was the first item from the menu purchased by each customer?

Answer SQL query:

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
[Try the codes here to check the answers!](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
