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

  
    SELECT ro.runner_id , COUNT (DISTINCT co.order_id) AS successful_deliveries
    FROM pizza_runner.customer_orders co
    INNER JOIN pizza_runner.runner_orders ro
    ON co.order_id = ro.order_id
    WHERE cancellation IS null
    GROUP BY ro.runner_id;
    
```

| runner_id | successful_deliveries |
| --------- | --------------------- |
| 1         | 1                     |
| 2         | 1                     |
| 3         | 1                     |




    How many of each type of pizza was delivered?
    
 ```SQL


    SELECT cu.pizza_id, COUNT(cu.pizza_id) AS total_number, pn.pizza_name AS  name
    FROM pizza_runner.customer_orders cu
    INNER JOIN pizza_runner.pizza_names pn
    ON cu.pizza_id = pn.pizza_id
    INNER JOIN pizza_runner.runner_orders ru
    ON cu.order_id = ru.runner_id
    WHERE cancellation IS null
    GROUP BY pn.pizza_name,  cu.pizza_id;
    
```

| pizza_id | total_number | name       |
| -------- | ------------ | ---------- |
| 2        | 1            | Vegetarian |
| 1        | 3            | Meatlovers |


    How many Vegetarian and Meatlovers were ordered by each customer?
```SQL

    SELECT cu.customer_id, COUNT(cu.pizza_id) AS total_number, pn.pizza_name AS  name
    FROM pizza_runner.customer_orders cu
    INNER JOIN pizza_runner.pizza_names pn
    ON cu.pizza_id = pn.pizza_id
    
    GROUP BY pn.pizza_name,  cu.customer_id
    ORDER BY customer_id;
    
```

| customer_id | total_number | name       |
| ----------- | ------------ | ---------- |
| 101         | 2            | Meatlovers |
| 101         | 1            | Vegetarian |
| 102         | 2            | Meatlovers |
| 102         | 1            | Vegetarian |
| 103         | 3            | Meatlovers |
| 103         | 1            | Vegetarian |
| 104         | 3            | Meatlovers |
| 105         | 1            | Vegetarian |


    What was the maximum number of pizzas delivered in a single order?
    For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
    How many pizzas were delivered that had both exclusions and extras?
    What was the total volume of pizzas ordered for each hour of the day?
    What was the volume of orders for each day of the week?
