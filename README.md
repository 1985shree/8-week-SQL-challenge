# 8-week-SQL-challenge: week 1

Here are the fun problems and solutions of [Danny Ma's 8 week SQL challenge!](https://8weeksqlchallenge.com/getting-started/)
These problems are great if one wants to learn and understand SQL in real life context.

First week:
check out the tables and problems here in [Danny's Diner](https://8weeksqlchallenge.com/case-study-1/)

Question1. What is the total amount each customer spent at the restaurant?

This requires a simple sum of price, aggregated for each customer.

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
Output:

| customer_id | total_amount |
| ----------- | ------------ |
| A           | 76           |
| B           | 74           |
| C           | 36           |


Question2. How many days has each customer visited the restaurant?

This is a bit tricky, because a customer may have ordered more than once on a day. To be sure we do not count a date twice, we should use 'DISTINCT' order dates! surely enough, you'll get two different answers if you try the code below with and without the keyword 'DISTINCT'.

```SQL
SELECT
	dannys_diner.sales.customer_id, 
    COUNT(DISTINCT order_date) as count
    FROM dannys_diner.sales
 GROUP BY dannys_diner.sales.customer_id
 ORDER BY dannys_diner.sales.customer_id;

```
Output:

| customer_id | count |
| ----------- | ----- |
| A           | 4     |
| B           | 6     |
| C           | 2     |



Question3. What was the first item from the menu purchased by each customer?

The queries start getting complicated. To get the 'first' item we need to use a rank function. I defined a new table as first_sales in the beginning and then chose RANK = 1 to truly separate out the first sales.

```SQL

WITH first_sales AS
	(SELECT
  		dannys_diner.menu.product_name,
    	dannys_diner.sales.customer_id,
    	RANK () OVER (ORDER BY dannys_diner.sales.order_date) as RANK
		FROM dannys_diner.sales
		INNER JOIN dannys_diner.menu
		ON dannys_diner.sales.product_id = dannys_diner.menu.product_id)
    SELECT product_name, customer_id
    FROM first_sales
    WHERE RANK = 1
    GROUP BY customer_id, product_name
    ORDER BY customer_id

```

Query output: 

| product_name | customer_id |
| ------------ | ----------- |
| curry        | A           |
| sushi        | A           |
| curry        | B           |
| ramen        | C           |




Question4. What is the most purchased item on the menu and how many times was it purchased by all customers?

This is again relatively simple.

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
Query output:

| times_ordered | product_name |
| ------------- | ------------ |
| 8             | ramen        |

Question 5.Which item was the most popular for each customer?

This requires grouping according to customer id and requires rank function to rank in descending order according to the number of times (i.e. count) a product has been ordered by each customer. 
```SQL

WITH Times_ordered AS
	(SELECT
  		RANK () OVER (ORDER BY COUNT(dannys_diner.sales.product_id) DESC) AS RANK,
    	dannys_diner.menu.product_name as item,
    	dannys_diner.sales.customer_id as customer, 
        COUNT(dannys_diner.sales.product_id) as count                             
    	FROM dannys_diner.sales
		INNER JOIN dannys_diner.menu
    	ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
        GROUP BY Customer, dannys_diner.menu.product_name, customer)
SELECT RANK, customer,  item, count
FROM Times_ordered   
WHERE RANK = 1;

```
looks like customers A and C are tied in the first place for ordering the same item exactly the same number of times.

| rank | customer | item  | count |
| ---- | -------- | ----- | ----- |
| 1    | C        | ramen | 3     |
| 1    | A        | ramen | 3     |



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
  	dannys_diner.menu.product_name as item,
    SUM(price) AS total_price,
    dannys_diner.sales.customer_id AS customer,     
    	CASE WHEN dannys_diner.menu.product_name LIKE '%sushi' THEN (20*SUM(price))
    	ELSE (10*SUM(price)) END AS points
               
    FROM dannys_diner.sales
	INNER JOIN dannys_diner.menu
    ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
                    
                     
GROUP BY customer, item
ORDER BY customer;




```
[Try the codes here to check the answers!](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
