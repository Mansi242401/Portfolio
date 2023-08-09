
-- Based on the provided schema, I created database, tables and columns using MYSQL Workbench.

-- creating database 
```sql
CREATE DATABASE dannys_diner;
```
-- selecting the database to use for extracting data
```sql
USE dannys_diner;
```

-- creating tables as per schema 
```sql
CREATE TABLE sales (
	customer_id VARCHAR(1),
   	order_date DATE,
    	product_id INT
    );
``` 
CREATE TABLE members (
	customer_id VARCHAR(1),
    join_date TIMESTAMP
    );
    
CREATE TABLE menu (
	product_id INT,
    product_name VARCHAR(5),
    price INT);
```
-- Inserting values in the above created tables    
INSERT INTO sales
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');

INSERT INTO members
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
  
INSERT INTO menu
VALUES 
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  

**Case Study Questions**
1. What is the total amount each customer spent at the restaurant?

**Query:**

SELECT 
	s.customer_id as cust_id,
    SUM(m.price) as total_amt
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
GROUP BY cust_id;

**Resulting table:**

| cust_id  |total_amt | 
|----------|----------|
| A        | 76       | 
| B        | 74       | 
| C        | 36       |


2.How many days has each customer visited the restaurant?

**Query:**

SELECT 
	customer_id as cust_id,
    COUNT(DISTINCT order_date) as days_visited
FROM sales
GROUP BY cust_id;

**Resulting table:**

| cust_id  |days_visited | 
|----------|-------------|
| A        | 4           | 
| B        | 6           | 
| C        | 2           |


3. What was the first item from the menu purchased by each customer?

**Query:**

WITH t0 as
(
SELECT
	s.customer_id as cust_id,
	DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS item_rank,
	m.product_name as item
FROM sales s
JOIN menu m 
ON s.product_id = m.product_id
)
SELECT 
	cust_id,
    item
FROM t0 
WHERE item_rank = 1;

**Resulting table:**

| cust_id  |item     | 
|----------|---------|
| A        | sushi   | 
| A        | curry   | 
| B        | curry   |
| C        | ramen   |
| C        | ramen   |


4. What is the most purchased item on the menu and how many times was it purchased by all customers?

**Query:**

SELECT
    m.product_name as item,
	COUNT(s.product_id) as item_count
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY item_count DESC
lIMIT 1;

**Resulting table**

| item   |item_count| 
|--------|----------|
| ramen  | 8        | 


5. Which item was the most popular for each customer?
-- Popularity here means the item ordered the most by each customer
**Query:**

WITH t0 as 
(
SELECT
	customer_id,
    product_id,
    COUNT(product_id),
    DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(product_id) DESC) as pop_rank
FROM sales
GROUP BY customer_id, product_id
)
SELECT 
	t0.customer_id as customer,
    m.product_name as item
FROM t0
JOIN menu m
ON t0.product_id = m.product_id
WHERE t0.pop_rank = 1
ORDER BY customer;

**Resulting table**

| customer |  item  | 
|----------|--------|
| A        | ramen  | 
| B        | sushi  | 
| B        | curry  |
| B        | ramen  |
| C        | ramen  |


6. Which item was purchased first by the customer after they became a member?

**Query:**

With t0 as 
(
SELECT 
	s.customer_id,
    m.product_name,
    DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS order_rank
FROM sales s
JOIN members mm
ON s.customer_id = mm.customer_id
JOIN menu m
ON s.product_id = m.product_id
WHERE s.order_date > mm.join_date
)
SELECT 
	customer_id as customer,
    product_name as item
FROM t0 
WHERE order_rank = 1;

**Resulting table**

| customer  |item   | 
|----------|--------|
| A        | ramen  | 
| B        | sushi  | 


7. Which item was purchased just before the customer became a member?

**Query:**

With t0 as 
(
SELECT 
	s.customer_id,
    m.product_name,
    s.order_date,
    mm.join_date,
    DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS order_rank
FROM sales s
JOIN members mm
ON s.customer_id = mm.customer_id
JOIN menu m
ON s.product_id = m.product_id
WHERE s.order_date < mm.join_date
)
SELECT 
	customer_id as customer,
    product_name as item
FROM t0 
WHERE order_rank = 1;

**Resulting table**

| customer  |item   | 
|----------|--------|
| A        | sushi  | 
| A        | curry  |
| B        | sushi  |

8. What is the total items and amount spent for each member before they became a member?

**Query:**

SELECT
	s.customer_id as customer,
	COUNT(s.product_id) as total_items,
    SUM(m.price) as total_amount
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
JOIN members mm
ON s.customer_id = mm.customer_id
WHERE s.order_date < mm.join_date
GROUP BY customer;

**Resulting table**

| customer | total_items | total_amt |
|----------|-------------|------------
| B        | 3           |  40       |
| A        | 2           |  25       |

9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

**Query:**

WITH t0 as 
(
SELECT
	s.customer_id as customer,
    m.price,
    m.product_name as item,
    CASE WHEN m.product_name = 'sushi' THEN m.price*20 
    ELSE m.price*10 END AS points
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
GROUP BY s.customer_id,m.price,m.product_name
)

SELECT 
	customer,
    SUM(points) as total_points
FROM t0
GROUP BY customer;

**Resulting table**

| customer  | total_points | 
|-----------|--------------|
| A         | 470          | 
| B         | 470          |
| C         | 120          |

10. In the first week after a customer joins the program (including their join date) 
they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
 
 **Query**
 
WITH t0 as 
(
SELECT
	s.customer_id as customer,
--     m.price AS price,
--     s.order_date,
--     mm.join_date,
--     m.product_name,
    CASE WHEN s.order_date BETWEEN mm.join_date AND DATE_ADD(mm.join_date, INTERVAL 7 DAY) OR m.product_name = 'sushi' THEN m.price*20 ELSE m.price*10 END AS points
FROM sales s
JOIN members mm
on s.customer_id = mm.customer_id
JOIN menu m
ON s.product_id = m.product_id
WHERE s.order_date < '2021-02-01'
)
SELECT 
	customer,
    SUM(points) AS points
FROM t0
GROUP BY customer;

**Resulting table**

| customer |points  | 
|----------|--------|
| B        | 940    | 
| A        | 1370   |

**Bonus Questions**

11. create basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL, 
with following fields - customer_id, order_date, product_name, price, member

**Query:**

SELECT 
    COALESCE(s.customer_id, mm.customer_id) AS customer_id,
    s.order_date,
    m.product_name,
    m.price,
    CASE WHEN mm.customer_id IS NULL OR COALESCE(s.order_date, '2020-12-31') < mm.join_date THEN 'N' ELSE 'Y' END AS member
FROM sales s
JOIN menu m ON s.product_id = m.product_id
LEFT JOIN members mm ON s.customer_id = mm.customer_id;

**Resulting table:**

| customer_id | order_date | product_name | price | member |
|-------------|------------|--------------|-------|--------|
| A           | 2021-01-01 |  sushi       | 10    | N      |
| A           | 2021-01-01 |  curry       | 15    | N      |
| A           | 2021-01-07 |  curry       | 15    | Y      |
| A	      | 2021-01-10 |  ramen	  | 12	  | Y      |
| A	      | 2021-01-11 |  ramen	  | 12	  | Y      |
| A	      | 2021-01-11 |  men	  | 12	  | Y      |
| B	      | 2021-01-01 |  curry	  | 15	  | N      |
| B	      | 2021-01-02 |  curry	  | 15	  | N      |
| B	      | 2021-01-04 |  sushi	  | 10	  | N      |
| B	      | 2021-01-11 |  sushi       | 10	  | Y      |
| B	      | 2021-01-16 |  ramen	  | 12	  | Y      |
| B	      | 2021-02-01 |  ramen	  | 12	  | Y      |
| C	      | 2021-01-01 |  ramen	  | 12	  | N      |
| C	      | 2021-01-01 |  ramen	  | 12	  | N      |
| C	      | 2021-01-07 |  ramen	  | 12	  | N      |


12. Rank All The Things
Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for 
non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

-- Ranking in this case is done based on chronological order.
**Query:-**

WITH t0 as
(
SELECT 
    COALESCE(s.customer_id, mm.customer_id) AS customer_id,
    s.order_date,
    m.product_name,
    m.price,
    CASE WHEN mm.customer_id IS NULL OR COALESCE(s.order_date, '2020-12-31') < mm.join_date THEN 'N' ELSE 'Y' END AS member
FROM sales s
JOIN menu m ON s.product_id = m.product_id
LEFT JOIN members mm ON s.customer_id = mm.customer_id
)
SELECT 
	customer_id,
    order_date,
    product_name,
    price,
    member,
    CASE WHEN member = 'N' THEN NULL ELSE DENSE_RANK() OVER (PARTITION BY customer_id,member ORDER BY order_date) END AS ranking
FROM t0;

**Resulting table:**

customer_id	order_date	product_name	price	member	ranking
	A	2021-01-01	sushi	         10	  N	 NULL
	A	2021-01-01	curry	         15	  N	 NULL
	A	2021-01-07	curry	         15 	  Y	 1
	A	2021-01-10	ramen	         12	  Y	 2
	A	2021-01-11	ramen	         12	  Y	 3
	A	2021-01-11	ramen	         12	  Y	 3
	B	2021-01-01	curry	         15	  N	 NULL
	B	2021-01-02	curry	         15	  N	 NULL
	B	2021-01-04	sushi	         10	  N	 NULL
	B	2021-01-11	sushi	         10	  Y	 1
	B	2021-01-16	ramen	         12	  Y	 2
	B	2021-02-01	ramen	         12	  Y	 3
	C	2021-01-01	ramen	         12	  N	 NULL
	C	2021-01-01	ramen	         12	  N	 NULL
	C	2021-01-07	ramen	         12	  N	 NULL



