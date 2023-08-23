Cleaning table `pizza_recipes`

```sql
-- change data type of column toppings from 'TEXT' to 'VARCHAR'
ALTER TABLE pizza_recipes
ALTER COLUMN toppings VARCHAR(64);
```
C. Ingredient Optimisation
In the following section, we will answer the questions related to ingredients and how can those be optimised to improve sales

1. What are the standard ingredients for each pizza?
first, we will explode the comma separated values in toppings column into separate rows

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
-- Joining temporary table with pizza_names table to get the pizza_names and then with pizza_toppings table to get the names of the toppings
(SELECT
pn.pizza_name as p_name,
CAST(pt.topping_name AS nvarchar(32)) as t_name
FROM pizza_names pn
JOIN ingredients i
ON pn.pizza_id =  i.pizza_id
JOIN pizza_toppings pt
ON i.split_toppings = pt.topping_id)
SELECT 
p_name,
STRING_AGG(t_name, ',') as Standard_toppings
FROM ingr
GROUP BY p_name;
```

2. What was the most commonly added extra?
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

3. What was the most common exclusion?
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

4. 
   


