### Case Study #2 - Pizza Runner

#### For this case study I have used MS SQL Server to execute the queries 

Create a database named pizza_runner
```sql
CREATE DATABASE pizza_runner;
```

Creating tables - The tables are created as per the schema and instructions in the project with some flaws in the datatype of certain tables, because in real world, we may encounter these issues
```sql
CREATE TABLE runner_orders (
	order_id INT,
    runner_id INT,
    pickup_time VARCHAR(19),
    distance VARCHAR(7),
    duration VARCHAR(10),
    cancellation VARCHAR(23)
    );
```
```sql
CREATE TABLE runners (
	runner_id INT,
    registration_date date
    );
```
```sql
CREATE TABLE customer_orders (
	order_id INT,
    customer_id INT,
    pizza_id INT,
    exclusions VARCHAR(4),
    extras VARCHAR(4),
    order_date DATETIME
    );
```
```sql
CREATE TABLE pizza_names (
	pizza_id INT,
    pizza_name TEXT
    );
```
```sql
CREATE TABLE pizza_recipes (
	pizza_id INT,
    toppings TEXT
    );
```
```sql
CREATE TABLE pizza_toppings (
	topping_id INT,
    topping_name TEXT
    );
```
Populating tables - Again, the data is not following normalisation forms, hence messy data is beinig fed here. 
However, in real world this should be complying the normalization forms. But, we can always encounter messy data when working with already populated dataset.
```sql
INSERT INTO runners 
VALUES
( 1, '2020-01-01'),
( 2, '2020-01-03'),
( 3, '2020-01-08'),
( 4, '2020-01-15');
```
```sql
INSERT INTO customer_orders
VALUES 
  ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
  ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
  ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
  ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
  ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
  ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
  ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
  ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
  ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
  ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
  ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');
 ``` 
 ```sql
INSERT INTO runner_orders
VALUES
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
  ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
  ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');
```
```sql
INSERT INTO pizza_names
VALUES
(1, 'Meat Lovers'),
(2, 'Vegetarian');
```
```sql
INSERT INTO pizza_recipes
VALUES
(1, '1,2,3,4,5,6,8,10'),
(2, '4,6,7,9,11,12');
```
```sql
INSERT INTO pizza_toppings
VALUES
(1, 'Bacon'),
(2, 'BBQ Sauce'),
(3, 'Beef'),
(4, 'Cheese'),
(5, 'Chicken'),
(6, 'Mushrooms'),
(7, 'Onions'),
(8, 'Pepperoni'),
(9, 'Peppers'),
(10, 'Salami'),
(11, 'Tomatoes'),
(12, 'Tomato Sauce');
```
Before we answer the metrics questions, let's identify the dirty data and clean the tables - customer_orders and runner_orders

**Query:**
```sql
SELECT * FROM customer_orders;
```
**Result:**
|order_id|customer_id|pizza_id|exclusions|extras|order_date|
|---|---|---|---|---|---|
|1|101|1|||2020-01-01 18:05:02.000|
|2|101|1|||2020-01-01 19:00:52.000|
|3|102|1|||2020-01-02 23:51:23.000|
|3|102|2||NULL|2020-01-02 23:51:23.000|
|4|103|1|4||2020-01-04 13:23:46.000|
|4|103|1|4||2020-01-04 13:23:46.000|
|4|103|2|4||2020-01-04 13:23:46.000|
|5|104|1|null|1|2020-01-08 21:00:29.000|
|6|101|2|null|null|2020-01-08 21:03:13.000|
|7|105|2|null|1|2020-01-08 21:20:29.000|
|8|102|1|null|null|2020-01-09 23:54:33.000|
|9|103|1|4|1, 5|2020-01-10 11:22:59.000|
|10|104|1|null|null|2020-01-11 18:34:49.000|
|10|104|1|2, 6|1, 4|2020-01-11 18:34:49.000|

<b>Identify issues in customer_orders <br></b>

**1. Issue:** We can clearly see that there are empty strings and 'null' strings in the extras and exclusions column.NULL is the standard way to indicate the absence of a value in a database column and is considered better practice than putting empty string. Hence, we will replace these with NULL. 

**Solution:**
Insert NULL values in columns - extras and exclusions replacing the empty strings and 'null' strings

**Query:**

```sql
UPDATE customer_orders
SET 	
	exclusions = CASE WHEN exclusions = '' OR exclusions = 'null' THEN NULL ELSE exclusions END,
    extras = CASE WHEN extras = '' OR extras = 'null' THEN NULL ELSE extras END;
```
**Test:**

```sql
SELECT * FROM customer_orders;
```
**Result:**

|order_id|customer_id|pizza_id|exclusions|extras|order_date|
|---|---|---|---|---|---|
|1|101|1|NULL|NULL|2020-01-01 18:05:02.000|
|2|101|1|NULL|NULL|2020-01-01 19:00:52.000|
|3|102|1|NULL|NULL|2020-01-02 23:51:23.000|
|3|102|2|NULL|NULL|2020-01-02 23:51:23.000|
|4|103|1|4|NULL|2020-01-04 13:23:46.000|
|4|103|1|4|NULL|2020-01-04 13:23:46.000|
|4|103|2|4|NULL|2020-01-04 13:23:46.000|
|5|104|1|NULL|1|2020-01-08 21:00:29.000|
|6|101|2|NULL|NULL|2020-01-08 21:03:13.000|
|7|105|2|NULL|1|2020-01-08 21:20:29.000|
|8|102|1|NULL|NULL|2020-01-09 23:54:33.000|
|9|103|1|4|1, 5|2020-01-10 11:22:59.000|
|10|104|1|NULL|NULL|2020-01-11 18:34:49.000|
|10|104|1|2, 6|1, 4|2020-01-11 18:34:49.000|

We can observe in the above result that all empty strings and 'null' strings are converted to NULL values<br>

**2. Issue:** We observe that there is a duplicate record for Order Id 4

**Solution:** Remove all Duplicate rows 

**Query:**
```sql
WITH CTE AS
(
SELECT 
        order_id,
        customer_id,
        pizza_id,
        COALESCE(exclusions, '0') AS exclusions,
        COALESCE(extras,'0') AS extras,
        order_date,
        ROW_NUMBER() OVER (PARTITION BY order_id, customer_id, pizza_id, exclusions, extras, order_date ORDER BY order_date) AS temp
    FROM customer_orders
)
DELETE FROM CTE WHERE temp > 1;
```
**Test:**
```sql
SELECT * FROM customer_orders;
```
**Result:**

|order_id|customer_id|pizza_id|exclusions|extras|order_date|
|---|---|---|---|---|---|
|1|101|1|NULL|NULL|2020-01-01 18:05:02.000|
|2|101|1|NULL|NULL|2020-01-01 19:00:52.000|
|3|102|1|NULL|NULL|2020-01-02 23:51:23.000|
|3|102|2|NULL|NULL|2020-01-02 23:51:23.000|
|4|103|1|4|NULL|2020-01-04 13:23:46.000|
|4|103|2|4|NULL|2020-01-04 13:23:46.000|
|5|104|1|NULL|1|2020-01-08 21:00:29.000|
|6|101|2|NULL|NULL|2020-01-08 21:03:13.000|
|7|105|2|NULL|1|2020-01-08 21:20:29.000|
|8|102|1|NULL|NULL|2020-01-09 23:54:33.000|
|9|103|1|4|1, 5|2020-01-10 11:22:59.000|
|10|104|1|NULL|NULL|2020-01-11 18:34:49.000|
|10|104|1|2, 6|1, 4|2020-01-11 18:34:49.000|


The resultiing table doesn't have any duplicate records

<b>Identify issues in runner_orders <br></b>

```sql
SELECT * FROM runner_orders;
```
**3.Issue:** The columns `pickup_time`, `distance`, `duration` and `cancellation` has empty strings and 'null' values

**Solution:** Insert NULL values in columns - `pickup_time`, `distance`, `duration` and `cancellation` replacing the empty strings and 'null' strings

**Query:**
```sql
UPDATE runner_orders
SET 	
	pickup_time = CASE WHEN pickup_time = '' OR pickup_time = 'null' THEN NULL ELSE pickup_time END,
    distance = CASE WHEN distance = '' OR distance = 'null' THEN NULL ELSE distance END,
    duration = CASE WHEN duration = '' OR duration = 'null' THEN NULL ELSE duration END,
    cancellation = CASE WHEN cancellation = '' OR cancellation = 'null' THEN NULL ELSE cancellation END;
```
**Test:**
```sql
SELECT * FROM runner_orders;
```
**Result:**
|order_id|runner_id|pickup_time|distance|duration|cancellation|
|---|---|---|---|---|---|
|1|1|2020-01-01 18:15:34|20km|32 minutes|NULL|
|2|1|2020-01-01 19:10:54|20km|27 minutes|NULL|
|3|1|2020-01-03 00:12:37|13.4km|20 mins|NULL|
|4|2|2020-01-04 13:53:03|23.4|40|NULL|
|5|3|2020-01-08 21:10:57|10|15|NULL|
|6|3|NULL|NULL|NULL|Restaurant Cancellation|
|7|2|2020-01-08 21:30:45|25km|25mins|NULL|
|8|2|2020-01-10 00:15:02|23.4 km|15 minute|NULL|
|9|2|NULL|NULL|NULL|Customer Cancellation|
|10|1|2020-01-11 18:50:20|10km|10minutes|NULL|

The above query results suggest that all empty stings and 'null' strings are replaced with NULL values

**4.Issue:** Remove extra string from distance and duration column

**Query:**
```sql
UPDATE runner_orders
SET distance = CASE
    WHEN PATINDEX('%[a-zA-Z]%', distance) > 0
        THEN REPLACE(distance, SUBSTRING(distance, PATINDEX('%[a-zA-Z]%', distance), LEN(distance)), '')
    ELSE ''
    END
WHERE PATINDEX('%[a-zA-Z]%', distance) > 0;
```
```sql
UPDATE runner_orders
SET duration = CASE
    WHEN PATINDEX('%[a-zA-Z]%', duration) > 0
        THEN REPLACE(duration, SUBSTRING(duration, PATINDEX('%[a-zA-Z]%', duration), LEN(duration)), '')
    ELSE ''
    END
WHERE PATINDEX('%[a-zA-Z]%', duration) > 0;
```
**Test:**
```sql
SELECT * FROM runner_orders;
```

From the above result, we can see that all unrequired strings like "kms" and "min" are removed from distance and duration columns


