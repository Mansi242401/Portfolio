-- Case Study #2 - Pizza Runner
For this case study I have used MS SQL Server to execute the queries 

-- Create a database named pizza_runner
```sql
CREATE DATABASE pizza_runner;
```

-- Creating tables - The tables are created as per the schema and instructions in the project with some flaws in the datatype of certain tables, 
-- because in real world, we may encounter these issues
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

-- Populating tables - Again, the data is not following normalisation forms, hence messy data is beinig fed here. 
-- However, in real world this should be complying the normalization forms. But, we can always encounter messy data when working with already populated dataset.
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
-- Before we answer the metrics questions, let's identify the dirty data and clean the tables - customer_orders and runner_orders
-- Identify issues in customer_orders

-- 1. Insert NULL values in columns - extras and exclusions replacing the empty strings and 'null' strings 
-- NULL is the standard way to indicate the absence of a value in a database column and is considered better practice than putting empty string

UPDATE customer_orders
SET 	
	exclusions = CASE WHEN exclusions = '' OR exclusions = 'null' THEN NULL ELSE exclusions END,
    extras = CASE WHEN extras = '' OR extras = 'null' THEN NULL ELSE extras END;


