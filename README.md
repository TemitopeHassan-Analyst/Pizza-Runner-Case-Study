# Pizza-Runner-Case-Study 2
## Business Problem
Danny is expanding his new Pizza Empire and at the same time, he wants to Uberize it, so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.
## 🧼 Data Cleaning & Transformation
🔨 Table: customer_orders
Looking at the customer_orders table below, we can see that there are

In the exclusions column, there are missing/ blank spaces ' ' and null values.
In the extras column, there are missing/ blank spaces ' ' and null values.
-- Drop and recreate customer_orders table
IF OBJECT_ID('customer_orders', 'U') IS NOT NULL
    DROP TABLE customer_orders;
```
CREATE TABLE customer_orders (
    order_id    INTEGER,
    customer_id INTEGER,
    pizza_id    INTEGER,
    exclusions  VARCHAR(10),
    extras      VARCHAR(10),
    order_time  DATETIME
);

INSERT INTO customer_orders
    (order_id, customer_id, pizza_id, exclusions, extras, order_time)
VALUES
    (1,  101, 1, '',     '',      '2020-01-01 18:05:02'),
    (2,  101, 1, '',     '',      '2020-01-01 19:00:52'),
    (3,  102, 1, '',     '',      '2020-01-02 23:51:23'),
    (3,  102, 2, '',     NULL,    '2020-01-02 23:51:23'),
    (4,  103, 1, '4',    '',      '2020-01-04 13:23:46'),
    (4,  103, 1, '4',    '',      '2020-01-04 13:23:46'),
    (4,  103, 2, '4',    '',      '2020-01-04 13:23:46'),
    (5,  104, 1, 'null', '1',     '2020-01-08 21:00:29'),
    (6,  101, 2, 'null', 'null',  '2020-01-08 21:03:13'),
    (7,  105, 2, 'null', '1',     '2020-01-08 21:20:29'),
    (8,  102, 1, 'null', 'null',  '2020-01-09 23:54:33'),
    (9,  103, 1, '4',    '1, 5',  '2020-01-10 11:22:59'),
    (10, 104, 1, 'null', 'null',  '2020-01-11 18:34:49'),
    (10, 104, 1, '2, 6', '1, 4',  '2020-01-11 18:34:49');
```
| order_id | customer_id | pizza_id | exclusions | extras | order_time          |
|----------|-------------|----------|------------|--------|---------------------|
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23 |
| 3        | 102         | 2        |            | NULL   | 2020-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46 |
| 5        | 104         | 1        | null       | 1      | 2020-01-08 21:00:29 |
| 6        | 101         | 2        | null       | null   | 2020-01-08 21:03:13 |
| 7        | 105         | 2        | null       | 1      | 2020-01-08 21:20:29 |
| 8        | 102         | 1        | null       | null   | 2020-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59 |
| 10       | 104         | 1        | null       | null   | 2020-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49 |

### Remove null values in exclusions and extras columns and replace with blank space
```
--Cleaning the customer_orders table
SELECT 
  order_id, 
  customer_id, 
  pizza_id, 
  CASE
    WHEN exclusions IS NULL OR exclusions = 'null' OR exclusions = '' THEN ''
    ELSE exclusions
  END AS exclusions,
  CASE
    WHEN extras IS NULL OR extras = 'null' OR extras = '' THEN ''
    ELSE extras
  END AS extras,
  order_time
INTO #customer_orders_temp
FROM customer_orders;

-- Verify
SELECT * FROM #customer_orders_temp;
```
This is how the clean customers_orders table looks like and we will use this table to run all our queries.' '.
| order_id | customer_id | pizza_id | exclusions | extras | order_time          |
|----------|-------------|----------|------------|--------|---------------------|
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23 |
| 3        | 102         | 2        |            |        | 2020-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46 |
| 5        | 104         | 1        |            | 1      | 2020-01-08 21:00:29 |
| 6        | 101         | 2        |            |        | 2020-01-08 21:03:13 |
| 7        | 105         | 2        |            | 1      | 2020-01-08 21:20:29 |
| 8        | 102         | 1        |            |        | 2020-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59 |
| 10       | 104         | 1        |            |        | 2020-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49 |

## 🔨 Table: runner_orders

```
-- Drop and recreate runner_orders table
IF OBJECT_ID('runner_orders', 'U') IS NOT NULL
    DROP TABLE runner_orders;

CREATE TABLE runner_orders (
    order_id     INTEGER,
    runner_id    INTEGER,
    pickup_time  VARCHAR(19),
    distance     VARCHAR(10),
    duration     VARCHAR(15),
    cancellation VARCHAR(30)
);

INSERT INTO runner_orders
    (order_id, runner_id, pickup_time, distance, duration, cancellation)
VALUES
    (1,  1, '2020-01-01 18:15:34', '20km',    '32 minutes',  ''),
    (2,  1, '2020-01-01 19:10:54', '20km',    '27 minutes',  ''),
    (3,  1, '2020-01-03 00:12:37', '13.4km',  '20 mins',     NULL),
    (4,  2, '2020-01-04 13:53:03', '23.4',    '40',          NULL),
    (5,  3, '2020-01-08 21:10:57', '10',      '15',          NULL),
    (6,  3, 'null',                'null',    'null',        'Restaurant Cancellation'),
    (7,  2, '2020-01-08 21:30:45', '25km',    '25mins',      'null'),
    (8,  2, '2020-01-10 00:15:02', '23.4 km', '15 minute',   'null'),
    (9,  2, 'null',                'null',    'null',        'Customer Cancellation'),
    (10, 1, '2020-01-11 18:50:20', '10km',    '10minutes',   'null');
```
| order_id | runner_id | pickup_time         | distance | duration     | cancellation            |
|----------|-----------|---------------------|----------|--------------|-------------------------|
| 1        | 1         | 2020-01-01 18:15:34 | 20km     | 32 minutes   |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20km     | 27 minutes   |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4km   | 20 mins      | NULL                    |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4     | 40           | NULL                    |
| 5        | 3         | 2020-01-08 21:10:57 | 10       | 15           | NULL                    |
| 6        | 3         | null                | null     | null         | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25km     | 25mins       | null                    |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4 km  | 15 minute    | null                    |
| 9        | 2         | null                | null     | null         | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10km     | 10minutes    | null                    |

### Our course of action to clean the table:

- In pickup_time column, remove nulls and replace with blank space ' '.
- In distance column, remove "km" and nulls and replace with blank space ' '.
- In duration column, remove "minutes", "minute" and nulls and replace with blank space ' '.
- In cancellation column, remove NULL and null and and replace with blank space ' '.

```
--Cleaning the runnerS_orders table
-- Drop temp table if it already exists
IF OBJECT_ID('tempdb..#runner_orders_temp') IS NOT NULL
    DROP TABLE #runner_orders_temp;

SELECT 
  order_id, 
  runner_id,  
  CASE
    WHEN pickup_time = 'null' OR pickup_time IS NULL THEN ''
    ELSE pickup_time
  END AS pickup_time,
  CASE
    WHEN distance = 'null' OR distance IS NULL THEN ''
    WHEN distance LIKE '%km' THEN TRIM(REPLACE(distance, 'km', ''))
    ELSE distance 
  END AS distance,
  CASE
    WHEN duration = 'null' OR duration IS NULL THEN ''
    WHEN duration LIKE '%mins'    THEN TRIM(REPLACE(duration, 'mins', ''))
    WHEN duration LIKE '%minute'  THEN TRIM(REPLACE(duration, 'minute', ''))
    WHEN duration LIKE '%minutes' THEN TRIM(REPLACE(duration, 'minutes', ''))
    ELSE duration
  END AS duration,
  CASE
    WHEN cancellation IS NULL OR cancellation = 'null' OR cancellation = '' THEN ''
    ELSE cancellation
  END AS cancellation
INTO #runner_orders_temp
FROM runner_orders;

-- Verify
SELECT * FROM #runner_orders_temp;
```
| order_id | runner_id | pickup_time         | distance | duration | cancellation            |
|----------|-----------|---------------------|----------|----------|-------------------------|
| 1        | 1         | 2020-01-01 18:15:34 | 20       | 32       |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20       | 27       |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4     | 20       |                         |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4     | 40       |                         |
| 5        | 3         | 2020-01-08 21:10:57 | 10       | 15       |                         |
| 6        | 3         |                     |          |          | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25       | 25       |                         |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4     | 15       |                         |
| 9        | 2         |                     |          |          | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10       | 10       |                         |
## A. Pizza Metrics
1. How many pizzas were ordered?
```
SELECT COUNT(*) AS pizza_order_count
FROM dbo.customer_orders;
```
| pizza_order_count |
|-------------------|
| 14                |

Insight:
14 pizzas were ordered in total across all orders
### 2. How many unique customer orders were made?

| unique_order_count |
|--------------------|
| 10                 |

Insight:
10 unique orders were placed in total — meaning across the 14 pizza rows in the table, they belong to 10 distinct orders.

### 3. How many successful orders were delivered by each runner?
```
SELECT 
  runner_id, 
  COUNT(order_id) AS successful_orders
FROM dbo.runner_orders
WHERE distance != '0'
GROUP BY runner_id;
```

| runner_id | successful_orders |
|-----------|-------------------|
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

- Runner 1 has 4 successful delivered orders.
- Runner 2 has 3 successful delivered orders.
- Runner 3 has 1 successful delivered order.
## 4. How many of each type of pizza were delivered?
```
SELECT 
  p.pizza_name, 
  COUNT(c.pizza_id) AS delivered_pizza_count
FROM dbo.customer_orders c
JOIN dbo.runner_orders AS r
  ON c.order_id = r.order_id
JOIN pizza_names AS p
  ON c.pizza_id = p.pizza_id
WHERE r.distance != '0'
  AND r.distance IS NOT NULL
  AND r.distance != ''
  AND LOWER(r.distance) != 'null'
GROUP BY p.pizza_name;
```
| pizza_name | delivered_pizza_count |
|------------|-----------------------|
| Meatlovers | 9                     |
| Vegetarian | 3                     |

Insight: There are 9 delivered Meatlovers pizzas and 3 Vegetarian pizzas.

## 5. How many Vegetarian and Meatlovers were ordered by each customer?
```
SELECT 
  c.customer_id, 
  p.pizza_name, 
  COUNT(p.pizza_name) AS order_count
FROM dbo.customer_orders AS c
JOIN pizza_names AS p
  ON c.pizza_id= p.pizza_id
GROUP BY c.customer_id, p.pizza_name
ORDER BY c.customer_id;
```
| customer_id | pizza_name | order_count |
|-------------|------------|-------------|
| 101         | Meatlovers | 2           |
| 101         | Vegetarian | 1           |
| 102         | Meatlovers | 2           |
| 102         | Vegetarian | 1           |
| 103         | Meatlovers | 3           |
| 103         | Vegetarian | 1           |
| 104         | Meatlovers | 3           |
| 105         | Vegetarian | 1           |

- Customer 101 ordered 2 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 102 ordered 2 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 103 ordered 3 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 104 ordered 3 Meatlovers pizza.
- Customer 105 ordered 1 Vegetarian pizza.
 ## 6. What was the maximum number of pizzas delivered in a single order?
```
WITH pizza_count_cte AS
(
  SELECT 
    c.order_id, 
    COUNT(c.pizza_id) AS pizza_per_order
  FROM dbo.customer_orders AS c
  JOIN dbo.runner_orders AS r
    ON c.order_id = r.order_id
  WHERE r.distance != '0'
    AND r.distance IS NOT NULL
    AND r.distance != ''
    AND LOWER(r.distance) != 'null'
  GROUP BY c.order_id
)
```
| pizza_count |
|-------------|
| 3           |

Insight: The maximum number of pizzas delivered in a single order is 3 pizzas.
##3 7. For each customer, how many delivered pizzas had at least 1 change 
-- and how many had no changes?
```
SELECT 
  c.customer_id,
  SUM(
    CASE 
      WHEN c.exclusions != '' OR c.extras != '' THEN 1
      ELSE 0
    END) AS at_least_1_change,
  SUM(
    CASE 
      WHEN c.exclusions = '' AND c.extras = '' THEN 1 
      ELSE 0
    END) AS no_change
FROM #customer_orders_temp AS c
JOIN #runner_orders_temp AS r
  ON c.order_id = r.order_id
WHERE r.distance != '0'
  AND r.distance IS NOT NULL
  AND r.distance != ''
  AND LOWER(r.distance) != 'null'
GROUP BY c.customer_id
ORDER BY c.customer_id;
```
| customer_id | at_least_1_change | no_change |
|-------------|-------------------|-----------|
| 101         | 0                 | 2         |
| 102         | 0                 | 3         |
| 103         | 3                 | 0         |
| 104         | 2                 | 1         |
| 105         | 1                 | 0         |

Insight:
- Customer 101 and 102 likes his/her pizzas per the original recipe.
- Customers 103, 104, and 105 have their own preference for pizza topping and requested at least 1 change (extra or exclusion topping) on their pizza.
### -8. How many pizzas were delivered that had both exclusions and extras?
```
SELECT  
  SUM(
    CASE 
      WHEN c.exclusions != '' AND c.extras != '' THEN 1
      ELSE 0
    END) AS pizza_count_w_exclusions_extras
FROM #customer_orders_temp AS c
JOIN #runner_orders_temp AS r
  ON c.order_id = r.order_id
WHERE r.distance != ''
  AND r.distance != '0'
  AND r.distance IS NOT NULL
  AND LOWER(r.distance) != 'null'
  AND c.exclusions != ''
  AND c.extras != '';
```
| pizza_count_w_exclusions_extras |
|---------------------------------|
| 1                               |

Insight:
Only 1 pizza was delivered with both extra and exclusion toppings. 
### 9. What was the total volume of pizzas ordered for each hour of the day?
```
SELECT 
  DATEPART(HOUR, order_time) AS hour_of_day, 
  COUNT(order_id) AS pizza_count
FROM #customer_orders_temp
GROUP BY DATEPART(HOUR, order_time)
ORDER BY hour_of_day;
```
| hour_of_day | pizza_count |
|-------------|-------------|
| 11          | 1           |
| 13          | 3           |
| 18          | 3           |
| 19          | 1           |
| 21          | 3           |
| 23          | 3           |

Insight:
- Highest volume of pizza ordered is at 13 (1:00 pm), 18 (6:00 pm) and 21 (9:00 pm).
- Lowest volume of pizza ordered is at 11 (11:00 am), 19 (7:00 pm) and 23 (11:00 pm).
### 10. What was the volume of orders for each day of the week?
```
SELECT 
  FORMAT(DATEADD(DAY, 2, order_time), 'dddd') AS day_of_week,
  COUNT(order_id) AS total_pizzas_ordered
FROM #customer_orders_temp
GROUP BY FORMAT(DATEADD(DAY, 2, order_time), 'dddd')
ORDER BY 
  CASE FORMAT(DATEADD(DAY, 2, order_time), 'dddd')
    WHEN 'Monday'    THEN 1
    WHEN 'Tuesday'   THEN 2
    WHEN 'Wednesday' THEN 3
    WHEN 'Thursday'  THEN 4
    WHEN 'Friday'    THEN 5
    WHEN 'Saturday'  THEN 6
    WHEN 'Sunday'    THEN 7
  END;
```
| day_of_week | total_pizzas_ordered |
|-------------|----------------------|
| Monday      | 5                    |
| Friday      | 5                    |
| Saturday    | 3                    |
| Sunday      | 1                    |
Insights:
- There are 5 pizzas ordered on Friday and Monday.
- There are 3 pizzas ordered on Saturday.
- There is 1 pizza ordered on Sunday.
## B. Runner and Customer Experience
1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```
SELECT 
  DATEPART(WEEK, registration_date) AS registration_week,
  COUNT(runner_id) AS runner_signup
FROM dbo.runners
GROUP BY DATEPART(WEEK, registration_date)
ORDER BY registration_date;
```
| registration_week | runner_signup |
|-------------------|---------------|
| 1                 | 2             |
| 2                 | 1             |
| 3                 | 1             |

Insight:
- In Week 1 of Jan 2021, 2 new runners signed up.
- In Week 2 and 3 of Jan 2021, 1 new runner signed up.

-- 12. What was the average time in minutes it took for each runner 
-- to arrive at Pizza Runner HQ to pickup the order?
### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```
WITH time_taken_cte AS
(
  SELECT 
    c.order_id, 
    c.order_time,
    CAST(r.pickup_time AS DATETIME) AS pickup_time, 
    DATEDIFF(MINUTE, c.order_time, CAST(r.pickup_time AS DATETIME)) AS pickup_minutes
  FROM #customer_orders_temp AS c
  JOIN #runner_orders_temp AS r
    ON c.order_id = r.order_id
  WHERE r.distance != ''
    AND r.distance != '0'
    AND r.distance IS NOT NULL
    AND LOWER(r.distance) != 'null'
  GROUP BY c.order_id, c.order_time, r.pickup_time
)

SELECT 
  AVG(pickup_minutes) AS avg_pickup_minutes
FROM time_taken_cte
WHERE pickup_minutes > 1;
```
| avg_pickup_minutes |
|--------------------|
| 16                 |
Insight:
The average time taken in minutes by runners to arrive at Pizza Runner HQ to pick up the order is 16 minutes.
