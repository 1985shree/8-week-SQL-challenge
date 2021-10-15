# 8-week-SQL-challenge: week 1

Here are the fun problems and solutions of [Danny Ma's 8 week SQL challenge!](https://8weeksqlchallenge.com/getting-started/)
These problems are great if one wants to learn and understand SQL in real life context.

First week:
check out the tables and problems here in [Danny's Diner](https://8weeksqlchallenge.com/case-study-1/)

Schema SQL.
```
CREATE SCHEMA dannys_diner;
SET search_path = dannys_diner;

CREATE TABLE sales (
  "customer_id" VARCHAR(1),
  "order_date" DATE,
  "product_id" INTEGER
);

INSERT INTO sales
  ("customer_id", "order_date", "product_id")
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
 

CREATE TABLE menu (
  "product_id" INTEGER,
  "product_name" VARCHAR(5),
  "price" INTEGER
);

INSERT INTO menu
  ("product_id", "product_name", "price")
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  

CREATE TABLE members (
  "customer_id" VARCHAR(1),
  "join_date" DATE
);

INSERT INTO members
  ("customer_id", "join_date")
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
  
```
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

This requires grouping according to customer id and requires rank function to rank in descending order according to the number of times (i.e. count) a product has been ordered by each customer. This time we will use PARTITION with respect to customers to get ranks for individual customers. First a new table is defined with the defined order of rank as 'RANK'. Choosing 'RANK' = 1 then selects the most purchased items. Rank will be same for customers who ordered same number of times.

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
looks like customers A and C are ordered the same item exactly the same number of times, while customer B tried everything the same number of times!


| rank | customer | item  | count |
| ---- | -------- | ----- | ----- |
| 1    | A        | ramen | 3     |
| 1    | B        | ramen | 2     |
| 1    | B        | curry | 2     |
| 1    | B        | sushi | 2     |
| 1    | C        | ramen | 3     |


Question6. Which item was purchased first by the customer after they became a member?

Similar to the previous problem, this too requires ranking with respect to order date and partitioning with respect to customers in the newly defined table. The added level of complication here is the joining of 3 tables.

```SQL
    WITH ranked_order_dates AS
    	(SELECT
        	RANK () OVER (PARTITION BY dannys_diner.sales.customer_id ORDER BY dannys_diner.sales.order_date),
         dannys_diner.sales.customer_id AS customers,
         dannys_diner.sales.order_date AS order_date,
         dannys_diner.menu.product_name AS item
         FROM dannys_diner.sales
         INNER JOIN dannys_diner.menu
         ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
         INNER JOIN dannys_diner.members
         ON dannys_diner.sales.customer_id = dannys_diner.members.customer_id
         WHERE order_date > join_date)
    SELECT customers, order_date, item, rank
    FROM ranked_order_dates
    WHERE RANK = 1;
    
 ```
 
 The result shows only customers A and B. What does it mean? we'll see soon.
 
 
| customers | order_date               | item  | rank |
| --------- | ------------------------ | ----- | ---- |
| A         | 2021-01-10T00:00:00.000Z | ramen | 1    |
| B         | 2021-01-11T00:00:00.000Z | sushi | 1    |



Question7. Which item was purchased just before the customer became a member?

This builds up in line with the previous question. The difference here is to order the dates in descending order (so that the latest date ranks first) and select order dates before joining date.

```SQL

WITH ranked_order_dates AS
    	(SELECT
        	RANK () OVER (PARTITION BY dannys_diner.sales.customer_id ORDER BY dannys_diner.sales.order_date DESC),
         dannys_diner.sales.customer_id AS customers,
         dannys_diner.sales.order_date AS order_date,
         dannys_diner.menu.product_name AS item
         FROM dannys_diner.sales
         INNER JOIN dannys_diner.menu
         ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
         INNER JOIN dannys_diner.members
         ON dannys_diner.sales.customer_id = dannys_diner.members.customer_id
         WHERE order_date < join_date)
    SELECT customers, order_date, item, rank
    FROM ranked_order_dates
    WHERE RANK = 1;

```

| customers | order_date               | item  | rank |
| --------- | ------------------------ | ----- | ---- |
| A         | 2021-01-01T00:00:00.000Z | sushi | 1    |
| A         | 2021-01-01T00:00:00.000Z | curry | 1    |
| B         | 2021-01-04T00:00:00.000Z | sushi | 1    |


The answers to Q 6 and 7 surprised me since  there was no customer C in the result. A quick check in the original 'members' table reveals that customer C is not a member at all!

Question8. What is the total items and amount spent for each member before they became a member?

This again requires joining of 3 tables and selecting order dates which are after dates of joining.

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


| item_counts | customer | total_price |
| ----------- | -------- | ----------- |
| 3           | B        | 40          |
| 2           | A        | 25          |


Question9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

This requires settimg a condition (CASE WHEN) for item names and I used 'LIKE '%sushi''. Also, points had to be summed up for each customer. To achieve that, I've defined a table called 'customer_points' with 'GROUP BY' applied to items. Then I selected columns from the new table and grouped by customers.

```SQL

WITH customer_points AS
    	(SELECT
      	dannys_diner.menu.product_name as item,
        SUM(price) AS total_price,
        dannys_diner.sales.customer_id AS customer,     
        	CASE WHEN dannys_diner.menu.product_name LIKE '%sushi' THEN (20*SUM(price))
        	ELSE (10*SUM(price)) END AS points
                   
        FROM dannys_diner.sales
    	INNER JOIN dannys_diner.menu
        ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
        GROUP BY item, customer)                
                         
    SELECT customer, SUM(points) as total_points
    FROM customer_points
    GROUP BY customer;
```

    

| customer | total_points |
| -------- | ------------ |
| B        | 940          |
| C        | 360          |
| A        | 860          |


Question10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

Again we have to use a complex clause, this time with 'BETWEEN' syntax to select dates within 1st week of joining. In the end one just needs to sum up the points for each customer!

```SQL
WITH customer_points AS
	(SELECT dannys_diner.members.customer_id as customer,
     
     	CASE WHEN order_date BETWEEN join_date AND join_date+7 THEN (2*SUM(price)) 
     	ELSE SUM(price) END AS points
    FROM dannys_diner.sales
    INNER JOIN dannys_diner.members 
    ON dannys_diner.sales.customer_id = dannys_diner.members.customer_id
    INNER JOIN dannys_diner.menu
    ON dannys_diner.menu.product_id = dannys_diner.sales.product_id
    GROUP BY customer, order_date, join_date)                                                                     
                                                                          
SELECT customer, SUM(points) AS total_points
FROM customer_points
GROUP BY customer
```


| customer | total_points |
| -------- | ------------ |
| B        | 96           |
| A        | 127          |



Bonus question 1. Join All The Things


[Try your own codes here to check the answers!](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
[check these ideal codes too!](https://github.com/AlysterF/8week-SQL-challenge/blob/main/Case%20Study%20%231%20-%20Danny's%20Diner/case_solutions.md)
