Cleaning table `pizza_recipes`

```sql
-- change data type of column toppings from 'TEXT' to 'VARCHAR'
ALTER TABLE pizza_recipes
ALTER COLUMN toppings VARCHAR(64);
```
C. Ingredient Optimisation
In the following section, we will answer the questions related to ingredients and how can those be optimised to improve sales

1. What are the standard ingredients for each pizza?<br>

first, we will explode the comma separated values in toppings column into separate rows

**Query:**

```sql
WITH ingredients as
(
select 
pizza_id, 
[value] AS split_toppings 
from pizza_recipes 
cross apply STRING_SPLIT(toppings,  ',')
),
ingr AS 
-- Joining temporary table with pizza_names table to get the pizza_names and then with pizza_toppings table to get the names of the toppings and creating another temporary table
(SELECT
pn.pizza_name as p_name,
CAST(pt.topping_name AS nvarchar(32)) as t_name
FROM pizza_names pn
JOIN ingredients i
ON pn.pizza_id =  i.pizza_id
JOIN pizza_toppings pt
ON i.split_toppings = pt.topping_id)
-- finally selecting from the second temp table to extract toppings names and aggregate the ingredients into comma separated string 
SELECT 
p_name,
STRING_AGG(t_name, ', ') as Standard_toppings
FROM ingr
GROUP BY p_name;
```
**Result:**

|p_name|Standard_toppings|
|---|---|
|Meat Lovers|Bacon, BBQ Sauce, Beef, Cheese, Chicken, Mushrooms, Pepperoni, Salami|
|Vegetarian|Cheese, Mushrooms, Onions, Peppers, Tomatoes, Tomato Sauce|

2. What was the most commonly added extra?

**Query:** 

```sql
WITH CTE AS (
select 
pizza_id,
CAST([value] AS Int) AS extras_split 
from customer_orders
cross apply STRING_SPLIT(extras,  ',')
),
pop_extras AS
(
SELECT TOP 1 
extras_split, 
count(extras_split) as popular_extras_count
FROM CTE 
GROUP BY extras_split
ORDER BY popular_extras_count DESC
) 
SELECT 
pt.topping_name,
p.popular_extras_count
FROM pop_extras p 
JOIN pizza_toppings pt 
ON p.extras_split = pt.topping_id
```
**Result:**

|topping_name|popular_extras_count|
|---|---|
|Bacon|4|

3. What was the most common exclusion?

**Query:**

```sql
WITH CTE AS (
select 
pizza_id,
CAST([value] AS Int) AS excl_split 
from customer_orders
cross apply STRING_SPLIT(exclusions,  ',')
),
pop_excl AS
(
SELECT TOP 1 
excl_split, 
count(excl_split) as popular_excl_count
FROM CTE 
GROUP BY excl_split
ORDER BY popular_excl_count DESC
) 
SELECT 
pt.topping_name,
p.popular_excl_count
FROM pop_excl p 
JOIN pizza_toppings pt 
ON p.excl_split = pt.topping_id
```
**Result:**

|topping_name|popular_excl_count|
|---|---|
|Cheese|3|


4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

**Query:**

```sql
-- Add a new column named - 'record_id' to assign a unique value to each row, this works as a primary key
ALTER TABLE customer_orders
ADD record_id INT IDENTITY(1,1);
-- splitting values in 'extras' column into rows and storing it in a temp table named extra_CTE
WITH extra_CTE AS 
(
    select 
    c.record_id as record_id,
    c.pizza_id,
    CAST(es.value AS Int) AS extras_split
    from customer_orders c
    cross apply STRING_SPLIT(c.extras,  ',') AS es 
), 
-- using the split values in 'extras' column to map the names of toppings from 'pizza_toppings' table and aggregating it back into a column called 'toppings'
-- This can be understood as replacing the numbers/toppings_ids in 'extras' column with 'topping_names' while keeping the same number of rows as in the cleaned 'customer_orders' table

extra_topp AS
(
SELECT 
    record_id
,'Extra '+ STRING_AGG(CAST(pt.topping_name AS VARCHAR(MAX)), ',') AS toppings
FROM extra_CTE c 
JOIN pizza_toppings pt 
ON pt.topping_id = c.extras_split
GROUP BY record_id
),
-- repeating the above logic for 'exclusions'
excl_CTE AS
(
    select 
    c.record_id as record_id,
    c.pizza_id,
    CAST(es.value AS Int) AS excl_split
    from customer_orders c
    cross apply STRING_SPLIT(c.exclusions,  ',') AS es 
),
excl_topp AS
(
SELECT 
    record_id
,'Exclude ' + STRING_AGG(CAST(pt.topping_name AS VARCHAR(MAX)), ',') AS toppings
FROM excl_CTE c 
JOIN pizza_toppings pt 
ON pt.topping_id = c.excl_split
GROUP BY record_id
)
,
-- The above two logics would return two tables one with rows where 'extras' are not null and another with rows where 'exclusions' are not null. But, since we need both we use 'UNION' to combine the data horizontally

EE_CTE AS
(
    SELECT * FROM extra_topp
    UNION
    SELECT * FROM excl_topp
)

--Finally, we write the logic to combine 'pizza_name' with 'extras' and 'exclusions'
SELECT 
c.record_id, 
CONCAT_WS(' - ', p.pizza_name, STRING_AGG(cte.toppings, ' - ')) as pizza_and_toppings
FROM customer_orders c 
JOIN pizza_names p ON c.pizza_id = p.pizza_id
LEFT JOIN EE_CTE cte ON c.record_id = cte.record_id
GROUP BY 
c.record_id,
p.pizza_name
ORDER BY c.record_id
```
**Result:**

|record_id|pizza_and_toppings|
|---|---|
|1|Meat Lovers|
|2|Meat Lovers|
|3|Meat Lovers|
|4|Vegetarian|
|5|Meat Lovers - Exclude Cheese|
|6|Vegetarian - Exclude Cheese|
|7|Meat Lovers - Extra Bacon|
|8|Vegetarian|
|9|Vegetarian - Extra Bacon|
|10|Meat Lovers|
|11|Meat Lovers - Exclude Cheese - Extra Bacon,Chicken|
|12|Meat Lovers|
|13|Meat Lovers - Exclude BBQ Sauce,Mushrooms - Extra Bacon,Cheese|

5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"



