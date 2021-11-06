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
    JOIN pizza_runner.runner_orders ro
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
    JOIN pizza_runner.pizza_names pn
    ON cu.pizza_id = pn.pizza_id
    JOIN pizza_runner.runner_orders ru
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

    SELECT cu.customer_id, pn.pizza_name AS  name, COUNT(cu.pizza_id) AS total_number
    FROM pizza_runner.customer_orders cu
    JOIN pizza_runner.pizza_names pn
    ON cu.pizza_id = pn.pizza_id
    
    GROUP BY pn.pizza_name,  cu.customer_id
    ORDER BY customer_id;
    
```

| customer_id | name       | total_number |
| ----------- | ---------- | ------------ |
| 101         | Meatlovers | 2            |
| 101         | Vegetarian | 1            |
| 102         | Meatlovers | 2            |
| 102         | Vegetarian | 1            |
| 103         | Meatlovers | 3            |
| 103         | Vegetarian | 1            |
| 104         | Meatlovers | 3            |
| 105         | Vegetarian | 1            |

 

    What was the maximum number of pizzas delivered in a single order?
    
```SQL

    WITH max_pizzas AS(
    	SELECT cu.order_id AS order, (COUNT(cu.pizza_id)) AS max_number
        FROM pizza_runner.customer_orders cu
        
        JOIN pizza_runner.runner_orders ru
        ON cu.order_id = ru.order_id
        WHERE cancellation IS null
        GROUP BY cu.order_id)
        
    SELECT MAX(max_number) AS highest_number
    FROM max_pizzas;
    
```

| highest_number |
| -------------- |
| 3              |

    For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```SQL

    WITH changes AS(
      SELECT co.customer_id as customer,
      COUNT(co.order_id) AS status_change_count,
      CASE WHEN co.exclusions IS null OR co.extras IS null THEN 'not changed'
      ELSE 'changed' END AS status 
        
      FROM pizza_runner.customer_orders co
      GROUP BY co.customer_id, co.exclusions, co.extras)
      
    SELECT customer, status, COUNT(status_change_count) AS change_count
    
    FROM changes
    GROUP BY customer, status
    ORDER BY customer;
    
```

| customer | status      | change_count |
| -------- | ----------- | ------------ |
| 101      | changed     | 2            |
| 102      | changed     | 2            |
| 102      | not changed | 1            |
| 103      | changed     | 2            |
| 104      | changed     | 3            |
| 105      | changed     | 1            |


    How many pizzas were delivered that had both exclusions and extras?
    
```SQL

    SELECT co.customer_id as customer,
    COUNT(*) AS order_count
        
    FROM pizza_runner.customer_orders co
    
    JOIN pizza_runner.runner_orders ro 
    ON co.order_id = ro.order_id
    WHERE co.exclusions IS NOT null and co.extras IS NOT null AND ro.cancellation IS NOT null
    GROUP BY co.customer_id
    ORDER BY co.customer_id;
    
```

| customer | order_count |
| -------- | ----------- |
| 101      | 3           |
| 102      | 1           |
| 103      | 1           |
| 104      | 2           |
| 105      | 1           |


    What was the total volume of pizzas ordered for each hour of the day?
    
 ```SQL

    SELECT EXTRACT(HOUR FROM co.order_time) as hour,
    COUNT(pizza_id) AS per_hour_volume
    
    FROM pizza_runner.customer_orders co
    GROUP BY hour
    ORDER BY hour;
    
```

| hour | per_hour_volume |
| ---- | --------------- |
| 11   | 1               |
| 13   | 3               |
| 18   | 3               |
| 19   | 1               |
| 21   | 3               |
| 23   | 3               |

    
    What was the volume of orders for each day of the week?
    
```SQL

    SELECT EXTRACT(DOW FROM co.order_time) as day_of_week,
    COUNT(pizza_id) AS per_day_volume
    
    FROM pizza_runner.customer_orders co
    GROUP BY day_of_week
    ORDER BY day_of_week;
    
```

| day_of_week | per_day_volume |
| ----------- | -------------- |
| 3           | 5              |
| 4           | 3              |
| 5           | 1              |
| 6           | 5              |


    
  
