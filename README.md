# 8-week-SQL-challenge: week 1

First week:
[Danny's Diner](https://8weeksqlchallenge.com/case-study-1/)

Here are the fun problems and solutions of [Danny Ma's 8 week SQL challenge!](https://8weeksqlchallenge.com/getting-started/)




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

Question5. Which item was the most popular for each customer?

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

Question6. Which item was purchased first by the customer after they became a member?

SQL query to find the answer:

```SQL

SELECT
  	dannys_diner.menu.product_name AS items,
    dannys_diner.members.customer_id AS Customer, 
    order_date
    FROM dannys_diner.members
	INNER JOIN dannys_diner.sales
    ON dannys_diner.sales.customer_id = dannys_diner.members.customer_id
    INNER JOIN dannys_diner.menu
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
    WHERE ORDER_DATE > JOIN_DATE
GROUP BY items, order_date, Customer
ORDER BY order_date ASC;

```

Question7. Which item was purchased just before the customer became a member?

SQL query to find the answer:

```SQL

SELECT
  	dannys_diner.menu.product_name AS items,
    dannys_diner.members.customer_id AS Customer, 
    order_date
    FROM dannys_diner.members
	INNER JOIN dannys_diner.sales
    ON dannys_diner.sales.customer_id = dannys_diner.members.customer_id
    INNER JOIN dannys_diner.menu
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
    WHERE ORDER_DATE < JOIN_DATE
GROUP BY items, order_date, Customer
ORDER BY order_date ASC;


```

The answers to Q 6 and 7 surprised me since  there was no customer C in the result. A quick check in the original 'members' table reveals that customer C is not a member at all!

Question8. What is the total items and amount spent for each member before they became a member?

SQL query to find the answer:

```SQL

SELECT
  	Count(dannys_diner.menu.product_name) AS item_counts,
    dannys_diner.members.customer_id AS Customer, 
    SUM(price) as total_price
    FROM dannys_diner.members
	INNER JOIN dannys_diner.sales
    ON dannys_diner.sales.customer_id = dannys_diner.members.customer_id
    INNER JOIN dannys_diner.menu
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
    WHERE order_date < JOIN_DATE
GROUP BY Customer;


```

Question9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

SQL query to find the answer:

```SQL

SELECT
  	dannys_diner.menu.product_name,
    dannys_diner.sales.customer_id AS customer, 
    COUNT(dannys_diner.menu.product_name) AS count,
    	CASE WHEN dannys_diner.menu.product_name == "ramen" THEN total_points = count*20
    	ELSE total_points = count*10 END AS total_points
    
    FROM dannys_diner.menu
	INNER JOIN dannys_diner.sales
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
    
GROUP BY customer, dannys_diner.menu.product_name
ORDER BY customer


```
[Try the codes here to check the answers!](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
