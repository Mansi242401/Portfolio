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






