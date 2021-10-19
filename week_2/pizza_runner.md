# 8-week-SQL-challenge: week 2
[Link to the challenge of this week](https://8weeksqlchallenge.com/case-study-2/)

# A. Pizza Metrics

    How many pizzas were ordered?
    
```SQL

    SELECT COUNT(order_id) AS pizza_count
    FROM pizza_runner.customer_orders;


```

| pizza_count |
| ----- |
| 14    |





    How many unique customer orders were made?
    
```SQL
    SELECT COUNT (DISTINCT order_id) AS unique_customer_orders
    FROM pizza_runner.customer_orders;
    
```

| unique_customer_orders |
| ---------------------- |
| 10                     |


    How many successful orders were delivered by each runner?
    
```SQL

    SELECT COUNT (DISTINCT co.order_id) AS successful_deliveries
    FROM pizza_runner.customer_orders co
    INNER JOIN pizza_runner.runner_orders ro
    ON co.order_id = ro.order_id
    WHERE cancellation IS null
    GROUP BY ro.runner_id;
    
```

| successful_deliveries |
| --------------------- |
| 1                     |
| 1                     |
| 1                     |




    How many of each type of pizza was delivered?
    How many Vegetarian and Meatlovers were ordered by each customer?
    What was the maximum number of pizzas delivered in a single order?
    For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
    How many pizzas were delivered that had both exclusions and extras?
    What was the total volume of pizzas ordered for each hour of the day?
    What was the volume of orders for each day of the week?
