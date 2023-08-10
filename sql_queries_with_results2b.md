### Case Study #2 - Pizza Runner
You can click on the specific question to navigate to that question's solution
#### Pizza Metrics

- [How many pizzas were ordered?](#How-many-pizzas-were-ordered?)
- [How many unique customer orders were made?](#How-many-unique-customer-orders-were-made?)
- [How many successful orders were delivered by each runner?](#How-many-successful-orders-were-delivered-by-each-runner?)
- [How many of each type of pizza was delivered?](#How-many-of-each-type-of-pizza-was-delivered?)
- [How many Vegetarian and Meatlovers were ordered by each customer?](#How-many-Vegetarian-and-Meatlovers-were-ordered-by-each-customer?)
- [What was the maximum number of pizzas delivered in a single order?](#What-was-the-maximum-number-of-pizzas-delivered-in-a-single-order?)
- [For each customer, how many delivered pizzas had at least 1 change and how many had no changes?](#For-each-customer,-how-many-delivered-pizzas-had-at-least-1-change-and-how-many-had-no-changes?)
- [How many pizzas were delivered that had both exclusions and extras?](#How-many-pizzas-were-delivered-that-had-both-exclusions-and-extras?)
- [What was the total volume of pizzas ordered for each hour of the day?](#What-was-the-total-volume-of-pizzas-ordered-for-each-hour-of-the-day?)
- [What was the volume of orders for each day of the week?](#What-was-the-volume-of-orders-for-each-day-of-the-week?)

#### Runner and Customer Experience

- [How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)](#1RCE)
- [What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?](#2RCE)
- [Is there any relationship between the number of pizzas and how long the order takes to prepare?](#3RCE)
- [What was the average distance travelled for each customer?](#4RCE)
- [What was the difference between the longest and shortest delivery times for all orders?](#5RCE)
- [What was the average speed for each runner for each delivery and do you notice any trend for these values?](#6RCE)
- [What is the successful delivery percentage for each runner?](#7RCE)

<a name="How-many-pizzas-were-ordered?"></a>
### How many pizzas were ordered?
**Query:**
```sqlSELECT 
	COUNT(pizza_id) as pizza_count
FROM customer_orders;
```
**Result:**
|pizza_count|
|---|
|13|

<a name="How-many-unique-customer-orders-were-made?"></a>
### How many unique customer orders were made?
**Query:**
```sql
SELECT 
	COUNT(DISTINCT order_id) AS unique_pizza_count 
FROM customer_orders;
```
**Result:**
|unique_pizza_count|
|---|
|10|

<a name="How-many-successful-orders-were-delivered-by-each-runner?"></a>
### How many successful orders were delivered by each runner?

Successful orders are those which are not cancelled by either party

**Query:**
```sql
SELECT 
	runner_id, 
	count(runner_id) AS pizza_count
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;
```
**Result:**
|runner_id|pizza_count|
|---|---|
|1|4|
|2|3|
|3|1|

Runner 1 has delivered the most orders, then runner 2 has delivered 3 orders.

<a name="How-many-of-each-type-of-pizza-was-delivered?"></a>
### How many of each type of pizza was delivered?

For this one, we need to first count all pizzas orders and then group them by the type

**Query:**
```sql
SELECT 
c.pizza_id, 
count(pizza_id) AS pizza_count
FROM customer_orders c
JOIN 
runner_orders r
ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY c.pizza_id;
```
**Result:**
|pizza_id|pizza_count|
|---|---|
|1|8|
|2|3|

Majority delivered pizzas are of type 1 which is Meat Lovers.We break it down further by each customer

<a name="How-many-Vegetarian-and-Meatlovers-were-ordered-by-each-customer?"></a>
### How many Vegetarian and Meatlovers were ordered by each customer?

For this one, we need to join the `pizza_names` table in order to get the names of the pizzas ordered and then convert the row values into columns and their count as row value using pivot

**Query:**
```sql
WITH CTE AS 
(
SELECT 
c.customer_id,
pn.pizza_name,
count(c.pizza_id) as pizza_count
FROM customer_orders c
JOIN pizza_names pn
ON c.pizza_id = pn.pizza_id
GROUP BY c.customer_id, pn.pizza_name
)

SELECT *
FROM CTE
PIVOT (
    SUM(pizza_count)
    FOR pizza_name IN ([Meat Lovers], [Vegetarian])
) AS PivotTable;
```
**Result:**
|customer_id|Meat Lovers|Vegetarian|
|---|---|---|
|101|2|1|
|102|2|1|
|103|2|1|
|104|3|NULL|
|105|NULL|1|

We can observe that Customer 101, 102 and 103 ordered 2 meat lovers and 1 vegetarian pizza each. Customer 104 ordered only 3 Meatlovers and 105 ordered 1 vegetarian pizza

<a name="What-was-the-maximum-number-of-pizzas-delivered-in-a-single-order?"></a>
### What was the maximum number of pizzas delivered in a single order?
First, we need to filter the rows where order was not cancelled as we are looking at only delivered pizzas.
Then, we count the pizzas and group them by order_id as we are looking for each order.

**Query:**
```sql
SELECT
c.order_id,
count(pizza_id) AS pizza_count
FROM 
customer_orders c
JOIN runner_orders r 
ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY c.order_id
ORDER BY pizza_count DESC;
```
**Result:**
|order_id|pizza_count|
|---|---|
|3|2|
|4|2|
|10|2|
|1|1|
|2|1|
|5|1|
|7|1|
|8|1|

We observe that a single order had maximum 2 pizzas ordered

<a name="For-each-customer,-how-many-delivered-pizzas-had-at-least-1-change-and-how-many-had-no-changes?"></a>
### For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
Here, the change could be in exclusions and extras, hence, we need to find the count of pizzas that where exclusions or/and extras are NOT NULL for counting changed pizzas, and NULL for these columns when counting not changed pizzas and then group them by customer_id as we need to find for each customer

**Query:**
```sql
SELECT customer_id,
	COUNT(CASE WHEN exclusions <> '' OR extras <> '' THEN 1 END) AS changed,
   COUNT(CASE WHEN exclusions IS NULL AND extras IS NULL THEN 1 END) AS unchanged 
FROM customer_orders c 
JOIN runner_orders r 
ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY customer_id
ORDER BY customer_id;
```
**Result:**
|customer_id|changed|unchanged|
|---|---|---|
|101|0|2|
|102|0|3|
|103|2|0|
|104|2|1|
|105|1|0|


<a name="How-many-pizzas-were-delivered-that-had-both-exclusions-and-extras?"></a>
### How many pizzas were delivered that had both exclusions and extras?
**Query:**
```sql
SELECT pizza_id,
	COUNT(CASE WHEN exclusions <> '' AND extras <> '' THEN 1 END) AS exclusions_extras_count
FROM customer_orders c 
JOIN runner_orders r 
ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY pizza_id
ORDER BY pizza_id;
```

***Result:**
|pizza_id|exclusions_extras_count|
|---|---|
|1|1|
|2|0|

<a name="What-was-the-total-volume-of-pizzas-ordered-for-each-hour-of-the-day?"></a>
### What was the total volume of pizzas ordered for each hour of the day?
In this one, first we will extract the hour from the `order_date` column and then find the pizza count and group it by hour
**Query:**
```sql
WITH CTE AS(
SELECT 
pizza_id,
DATEPART(hour, [order_date]) AS order_hour
FROM customer_orders
)
SELECT order_hour,
COUNT(pizza_id) as pizza_count
FROM CTE 
GROUP BY order_hour;
```

**Result:**
|order_hour|pizza_count|
|---|---|
|11|1|
|13|2|
|18|3|
|19|1|
|21|3|
|23|3|

We observe that majority pizzas were ordered for dinner.

<a name="What-was-the-volume-of-orders-for-each-day-of-the-week?"></a>
### What was the volume of orders for each day of the week?
First step in this one is to find the day of the week for all records and then count the orders and group them by the day of the week

**Query:**
```sql
WITH CTE AS
(
SELECT 
pizza_id,
CASE DATEPART(DW, order_date)
        WHEN 1 THEN 'Sunday'
        WHEN 2 THEN 'Monday'
        WHEN 3 THEN 'Tuesday'
        WHEN 4 THEN 'Wednesday'
        WHEN 5 THEN 'Thursday'
        WHEN 6 THEN 'Friday'
        WHEN 7 THEN 'Saturday'
    END AS DayOfWeek
FROM customer_orders
)
SELECT DayOfWeek,
COUNT(pizza_id) as pizza_count
FROM CTE 
GROUP BY DayOfWeek;
```

**Result:**
|DayOfWeek|pizza_count|
|---|---|
|Friday|1|
|Saturday|4|
|Thursday|3|
|Wednesday|5|

We observe that majority orders were made on Wednesdays and then on Saturday and Thursday.

Next, we start the second part of the Case Study
#### B. Runner and Customer Experience

<a name="1RCE"></a>
### How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
WITH CTE AS 
(
    SELECT 
*, 
CASE WHEN registration_date BETWEEN '2020-01-01' AND '2020-01-07' THEN 'WEEK 1'
WHEN registration_date BETWEEN '2020-01-08' AND '2020-01-14' THEN 'WEEK 2'
WHEN registration_date BETWEEN '2020-01-15' AND '2020-01-23' THEN 'WEEK 3'
ELSE 'WEEK 4' END AS week_of_the_month
FROM runners
)
SELECT week_of_the_month,
COUNT(runner_id) as runner_count
FROM CTE 
GROUP BY week_of_the_month;
```

**Result:**
|week_of_the_month|runner_count|
|---|---|
|WEEK 1|2|
|WEEK 2|1|
|WEEK 3|1|

<a name="2RCE"></a>
### What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
The time taken by runner would be the difference between the time when the order was placed and the time when order was picked
**Query:**
```sql
SELECT
r.runner_id,
AVG(DATEDIFF(mi, c.order_date, r.pickup_time)) AS avg_time_diff
from runner_orders r
JOIN customer_orders c
ON r.order_id = c.order_id
GROUP BY r.runner_id;
```

**Result:**
|runner_id|avg_time_diff|
|---|---|
|1|15|
|2|22|
|3|10|

<a name="3RCE"></a>
### Is there any relationship between the number of pizzas and how long the order takes to prepare?
Again the time to prepare the pizza is the difference between the time when it was ordered and the time it was picked up, next we count the number of pizzas in each order and then find the avg time taken and group it by the number of pizzas ordered in each order
**Query:**
```sql
WITH t0 AS
(
    SELECT
order_id,
COUNT(pizza_id) as pizza_count
FROM customer_orders
GROUP BY order_id
),
t1 AS
(
SELECT
r.order_id,
DATEDIFF(mi, c.order_date, r.pickup_time) AS time_diff
from runner_orders r
JOIN customer_orders c
ON r.order_id = c.order_id
WHERE r.cancellation IS NULL
), t2 AS
(
SELECT 
DISTINCT 
t0.order_id,
t0.pizza_count as pizza_count,
t1.time_diff as time_diff
FROM t0 
join t1 ON t0.order_id = t1.order_id
)
SELECT t2.pizza_count,
AVG(t2.time_diff) as avg_time_taken
FROM t2
GROUP BY t2.pizza_count;
```
**Result:**
|pizza_count|avg_time_taken|
|---|---|
|1|12|
|2|22|

We do find that time to prepare is roughly 80 % more when 2 pizzas were ordered as compared to when one pizza was ordered

<a name="4RCE"></a>
### What was the average distance travelled for each customer?

**Query:**
```sql
SELECT c.customer_id, ROUND(AVG(r.distance),2) as avg_distance_KM FROM customer_orders c
JOIN runner_orders r on c.order_id = r.order_id
WHERE r.cancellation IS NULL
GROUP BY c.customer_id;
```

**Result:**
|customer_id|avg_distance_KM|
|---|---|
|101|20.000000|
|102|16.730000|
|103|23.400000|
|104|10.000000|
|105|25.000000|

<a name="5RCE"></a>
### What was the difference between the longest and shortest delivery times for all orders?
**Query:**
```sql
SELECT MAX(duration) - MIN(duration) AS delivery_time_diff 
FROM runner_orders;
```

**Result:**
|delivery_time_diff|
|---|
|30|

<a name="6RCE"></a>
### What was the average speed for each runner for each delivery and do you notice any trend for these values?
For this one, we will first calculate the speed by using the formula - distance/time since we are finding for each order, we will use distinct before order_id

**Query:**
```sql
WITH CTE AS
(
SELECT DISTINCT order_id, runner_id, distance, duration
FROM runner_orders
WHERE duration IS NOT NULL
)
SELECT order_id,
runner_id,
ROUND((CAST(distance AS FLOAT) / CAST(duration AS FLOAT)) * 60, 2) as avg_speed
FROM CTE
```
**Result:**
|order_id|runner_id|avg_speed|
|---|---|---|
|1|1|37.5|
|2|1|44.44|
|3|1|40.2|
|4|2|35.1|
|5|3|40|
|7|2|60|
|8|2|93.6|
|10|1|60|

<a name="7RCE"></a>
### What is the successful delivery percentage for each runner?
successful delivery percentage would be orders that are not cancelled divided by the total orders and converted into percentage and then grouped for each runner

**Query:**
```sql
WITH cancellation_counter AS (
SELECT
	runner_id,
    CASE
    	WHEN cancellation IS NULL OR cancellation = 'NaN' THEN 1
	ELSE 0
    END AS no_cancellation_count,
    CASE
    	WHEN cancellation IS NOT NULL OR cancellation != 'NaN' THEN 1
	ELSE 0
    END AS cancellation_count
FROM runner_orders
) 
,t0 AS
( 
SELECT 
	runner_id,
    sum(no_cancellation_count) AS no_can_ct,
    SUM(cancellation_count) AS can_ct,
    SUM(no_cancellation_count) + SUM(cancellation_count) AS total_orders
FROM cancellation_counter
GROUP BY runner_id
)
SELECT runner_id, 
(CAST(no_can_ct AS float)/total_orders)*100 as ratio
FROM t0;
```

**Result:**
|runner_id|ratio|
|---|---|
|1|100|
|2|75|
|3|50|

the results suggest that none of the orders were cancelled for Runner 1 and hence he successfully delivered all orders, runner 2 delivered 75% of all orders and runner 3 delivered 50% of all orders


