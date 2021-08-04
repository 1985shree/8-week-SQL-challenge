# 8-week-SQL-challenge
This lists the problems and solutions of [Danny Ma's 8 week fun SQL challenge!](https://8weeksqlchallenge.com/getting-started/)

First week:
[Danny's Diner](https://8weeksqlchallenge.com/case-study-1/)

Questions:
1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent for each member before they became a member?
9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

Answer SQL queries:
1.

```

SELECT
  	dannys_diner.sales.customer_id,
    SUM(price) AS total_amount
	FROM dannys_diner.sales
	INNER JOIN dannys_diner.menu
	ON dannys_diner.sales.product_id = dannys_diner.menu.product_id
GROUP BY dannys_diner.sales.customer_id
ORDER BY dannys_diner.sales.customer_id;

```

[Try the code here:](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

2.

```
SELECT
	dannys_diner.sales.customer_id, 
    COUNT(order_date) as count
    FROM dannys_diner.sales
 GROUP BY dannys_diner.sales.customer_id
 ORDER BY dannys_diner.sales.customer_id;

[Try the code here:](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)

```
[Try the code here:](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138)
