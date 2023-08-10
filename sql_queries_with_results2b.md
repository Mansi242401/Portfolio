### Case Study #2 - Pizza Runner
#### Pizza Metrics

- [How many pizzas were ordered?](#How-many-pizzas-were-ordered?)
- [How many unique customer orders were made?](#How-many-unique-customer-orders-were-made?)
- [How many successful orders were delivered by each runner?](#How-many-successful-orders-were-delivered-by-each-runner?)
- [How many of each type of pizza was delivered?](#How-many-of-each-type-of-pizza-was-delivered?)
- [How many Vegetarian and Meatlovers were ordered by each customer?](#How-many-Vegetarian-and-Meatlovers-were-ordered-by-each-customer?)
- [What was the maximum number of pizzas delivered in a single order?](#What-was-the-maximum-number-of-pizzas-delivered-in-a-single-order?)
- [For each customer, how many delivered pizzas had at least 1 change and how many had no changes?](###For-each-customer,-how-many-delivered-pizzas-had-at-least-1-change-and-how-many-had-no-changes?)
- [How many pizzas were delivered that had both exclusions and extras?](###How-many-pizzas-were-delivered-that-had-both-exclusions-and-extras?)
- [What was the total volume of pizzas ordered for each hour of the day?](###What-was-the-total-volume-of-pizzas-ordered-for-each-hour-of-the-day?)
- [What was the volume of orders for each day of the week?](###What-was-the-total-volume-of-pizzas-ordered-for-each-hour-of-the-day?)


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

### How many successful orders were delivered by each runner?

Successful orders are those which are not cancelled by either party

**Query:**
```sql
SELECT 
	runner_id, 
	count(runner_id) 
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;
```
**Result:**
|runner_id|(No column name)|
|---|---|
|1|4|
|2|3|
|3|1|

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














### What was the total volume of pizzas ordered for each hour of the day?



