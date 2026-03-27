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
FROM customer_orders_temp;
``
| pizza_order_count |
|-------------------|
| 14                |
Insight:
14 pizzas were ordered across in total across all orders
