Cleaning table `pizza_recipes`

```sql
-- change data type of column toppings from 'TEXT' to 'VARCHAR'
ALTER TABLE pizza_recipes
ALTER COLUMN toppings VARCHAR(64);
```
1. What are the standard ingredients for each pizza?
first, we will explode the comma separated values in toppings column into separate rows

```sql
-- creating a temproray table with exploded comma separated values 
WITH ingredients as
(
select 
pizza_id, 
[value] AS split_toppings 
from pizza_recipes 
cross apply STRING_SPLIT(toppings,  ',')
)
-- Joining temporary table with pizza_names table to get the pizza_names and then with pizza_toppings table to get the names of the toppings 
SELECT
pn.pizza_name,
pt.topping_name
FROM pizza_names pn
JOIN ingredients i
ON pn.pizza_id =  i.pizza_id
JOIN pizza_toppings pt
ON i.split_toppings = pt.topping_id
```

2. What was the most commonly added extra?




