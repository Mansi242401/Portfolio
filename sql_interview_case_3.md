A database has tables - PC, Laptop and printer. 
Use the following script to create and insert values in the tables

```sql
CREATE TABLE PC (
    code INT,
    model VARCHAR(64),
    type VARCHAR(64),
    speed INT,
    ram INT,
    hd INT,
    cd VARCHAR(32),
    price DECIMAL(10, 2)
)


CREATE TABLE Laptop (
    code INT,
    model VARCHAR(64),
    speed INT,
    ram INT,
    hd INT,
    screen VARCHAR(64),
    price DECIMAL(10, 2)
)

CREATE TABLE printer (
    code INT,
    model VARCHAR(64),
    color VARCHAR(64),
    type VARCHAR(64),
    price DECIMAL(10, 2)
)

INSERT INTO PC (code, model, type, speed, ram, hd, cd, price)
VALUES
    (1, 'PC_Model_A', 'Desktop', 2500, 8, 1000, 'DVD-ROM', 599.99),
    (2, 'PC_Model_B', 'Laptop', 1800, 16, 512, 'CD-RW', 799.50),
    (3, 'PC_Model_C', 'Desktop', 3000, 16, 2000, 'DVD-RW', 1199.75),
    (4, 'PC_Model_D', 'Laptop', 2200, 32, 1024, 'None', 1499.00),
    (5, 'PC_Model_E', 'Desktop', 2700, 8, 500, 'DVD-ROM', 699.25),
    (6, 'PC_Model_F', 'Laptop', 2000, 8, 256, 'None', 499.99),
    (7, 'PC_Model_G', 'Desktop', 2800, 16, 1000, 'CD-RW', 799.00),
    (8, 'PC_Model_H', 'Laptop', 2400, 32, 512, 'DVD-RW', 899.50),
    (9, 'PC_Model_I', 'Desktop', 3100, 16, 1500, 'DVD-ROM', 999.00),
    (10, 'PC_Model_J', 'Laptop', 1900, 8, 500, 'None', 599.75),
    (11, 'PC_Model_K', 'Desktop', 2600, 32, 2000, 'DVD-RW', 1399.25),
    (12, 'PC_Model_L', 'Laptop', 2300, 16, 1024, 'CD-RW', 1199.99),
    (13, 'PC_Model_M', 'Desktop', 2900, 8, 256, 'DVD-ROM', 599.00),
    (14, 'PC_Model_N', 'Laptop', 2100, 32, 1000, 'DVD-RW', 999.50),
    (15, 'PC_Model_O', 'Desktop', 2700, 16, 500, 'None', 699.75),
    (16, 'PC_Model_P', 'Laptop', 1800, 8, 1500, 'CD-RW', 799.25),
    (17, 'PC_Model_Q', 'Desktop', 3200, 32, 512, 'DVD-ROM', 1199.00),
    (18, 'PC_Model_R', 'Laptop', 2500, 16, 2000, 'DVD-RW', 1499.99),
    (19, 'PC_Model_S', 'Desktop', 2200, 8, 1024, 'None', 799.00),
    (20, 'PC_Model_T', 'Laptop', 3000, 32, 256, 'CD-RW', 999.50);

INSERT INTO Laptop (code, model, speed, ram, hd, screen, price)
VALUES
    (1, 'Laptop_Model_A', 1800, 8, 512, '15.6-inch', 799.99),
    (2, 'Laptop_Model_B', 2200, 16, 1024, '14-inch', 1099.50),
    (3, 'Laptop_Model_C', 2000, 8, 256, '13.3-inch', 599.75),
    (4, 'Laptop_Model_D', 2400, 32, 512, '15-inch', 1499.00),
    (5, 'Laptop_Model_E', 2600, 16, 1000, '17.3-inch', 1299.25),
    (6, 'Laptop_Model_F', 2100, 8, 512, '14-inch', 699.99),
    (7, 'Laptop_Model_G', 1800, 16, 256, '13-inch', 599.00),
    (8, 'Laptop_Model_H', 2300, 32, 1024, '15.6-inch', 1499.50),
    (9, 'Laptop_Model_I', 2500, 8, 256, '13.3-inch', 799.00),
    (10, 'Laptop_Model_J', 2000, 16, 512, '14-inch', 999.75),
    (11, 'Laptop_Model_K', 2400, 8, 1000, '15.6-inch', 1199.25),
    (12, 'Laptop_Model_L', 2800, 32, 256, '13.3-inch', 1499.99),
    (13, 'Laptop_Model_M', 2200, 16, 512, '15-inch', 1099.00),
    (14, 'Laptop_Model_N', 1900, 8, 1024, '14-inch', 999.50),
    (15, 'Laptop_Model_O', 2700, 32, 512, '17.3-inch', 1399.75),
    (16, 'Laptop_Model_P', 2600, 16, 256, '13-inch', 1199.25),
    (17, 'Laptop_Model_Q', 2300, 8, 1000, '15.6-inch', 1299.00),
    (18, 'Laptop_Model_R', 2000, 32, 512, '14-inch', 999.99),
    (19, 'Laptop_Model_S', 2500, 16, 256, '13.3-inch', 799.00),
    (20, 'Laptop_Model_T', 2800, 8, 1024, '15-inch', 1499.50),
    (21, 'Laptop_Model_U', 2200, 8, 512, '14-inch', 899.99),
    (22, 'Laptop_Model_V', 1800, 16, 1000, '15.6-inch', 1199.00),
    (23, 'Laptop_Model_W', 2700, 32, 256, '13.3-inch', 1399.50),
    (24, 'Laptop_Model_X', 2100, 8, 512, '15-inch', 999.00),
    (25, 'Laptop_Model_Y', 2400, 16, 1024, '14-inch', 1199.75),
    (26, 'Laptop_Model_Z', 2000, 32, 512, '17.3-inch', 1499.25);
    

INSERT INTO printer (code, model, color, type, price)
VALUES
    (1, 'Printer_Model_A', 'Black', 'Laser', 199.99),
    (2, 'Printer_Model_B', 'Color', 'Inkjet', 149.50),
    (3, 'Printer_Model_C', 'Black', 'Laser', 249.75),
    (4, 'Printer_Model_D', 'Color', 'Inkjet', 129.00),
    (5, 'Printer_Model_E', 'Black', 'Dot Matrix', 99.25),
    (6, 'Printer_Model_F', 'Color', 'Inkjet', 179.99),
    (7, 'Printer_Model_G', 'Black', 'Laser', 299.00),
    (8, 'Printer_Model_H', 'Color', 'Inkjet', 199.50),
    (9, 'Printer_Model_I', 'Black', 'Laser', 349.00),
    (10, 'Printer_Model_J', 'Color', 'Inkjet', 229.75),
    (11, 'Printer_Model_K', 'Black', 'Laser', 199.25),
    (12, 'Printer_Model_L', 'Color', 'Inkjet', 149.99),
    (13, 'Printer_Model_M', 'Black', 'Dot Matrix', 179.00),
    (14, 'Printer_Model_N', 'Color', 'Inkjet', 299.50),
    (15, 'Printer_Model_O', 'Black', 'Laser', 249.75),
    (16, 'Printer_Model_P', 'Color', 'Inkjet', 129.25),
    (17, 'Printer_Model_Q', 'Black', 'Dot Matrix', 89.00),
    (18, 'Printer_Model_R', 'Color', 'Inkjet', 199.99),
    (19, 'Printer_Model_S', 'Black', 'Laser', 299.00),
    (20, 'Printer_Model_T', 'Color', 'Inkjet', 179.50);
```
**Question 1:** Combine the three tables and select the unique models and their avg price ?

**Query:**
```sql
SELECT 
DISTINCT code, 
model,
AVG(price) as avg_price
FROM PC
GROUP BY code, model
UNION 
SELECT 
DISTINCT code, 
model,
AVG(price) as avg_price
FROM Laptop
GROUP BY code, model
UNION
SELECT 
DISTINCT code, 
model,
AVG(price) as avg_price
FROM printer
GROUP BY code,model
```

**Result:**

|code|model|avg_price|
|---|---|---|
|1|Laptop_Model_A|799.990000|
|1|PC_Model_A|599.990000|
|1|Printer_Model_A|199.990000|
|2|Laptop_Model_B|1099.500000|
|2|PC_Model_B|799.500000|
|2|Printer_Model_B|149.500000|
|3|Laptop_Model_C|599.750000|
|3|PC_Model_C|1199.750000|
|3|Printer_Model_C|249.750000|
|4|Laptop_Model_D|1499.000000|
|4|PC_Model_D|1499.000000|
|4|Printer_Model_D|129.000000|
|5|Laptop_Model_E|1299.250000|
|5|PC_Model_E|699.250000|
|5|Printer_Model_E|99.250000|
|6|Laptop_Model_F|699.990000|
|6|PC_Model_F|499.990000|
|6|Printer_Model_F|179.990000|
|7|Laptop_Model_G|599.000000|
|7|PC_Model_G|799.000000|
|7|Printer_Model_G|299.000000|
|8|Laptop_Model_H|1499.500000|
|8|PC_Model_H|899.500000|
|8|Printer_Model_H|199.500000|
|9|Laptop_Model_I|799.000000|
|9|PC_Model_I|999.000000|
|9|Printer_Model_I|349.000000|
|10|Laptop_Model_J|999.750000|
|10|PC_Model_J|599.750000|
|10|Printer_Model_J|229.750000|
|11|Laptop_Model_K|1199.250000|
|11|PC_Model_K|1399.250000|
|11|Printer_Model_K|199.250000|
|12|Laptop_Model_L|1499.990000|
|12|PC_Model_L|1199.990000|
|12|Printer_Model_L|149.990000|
|13|Laptop_Model_M|1099.000000|
|13|PC_Model_M|599.000000|
|13|Printer_Model_M|179.000000|
|14|Laptop_Model_N|999.500000|
|14|PC_Model_N|999.500000|
|14|Printer_Model_N|299.500000|
|15|Laptop_Model_O|1399.750000|
|15|PC_Model_O|699.750000|
|15|Printer_Model_O|249.750000|
|16|Laptop_Model_P|1199.250000|
|16|PC_Model_P|799.250000|
|16|Printer_Model_P|129.250000|
|17|Laptop_Model_Q|1299.000000|
|17|PC_Model_Q|1199.000000|
|17|Printer_Model_Q|89.000000|
|18|Laptop_Model_R|999.990000|
|18|PC_Model_R|1499.990000|
|18|Printer_Model_R|199.990000|
|19|Laptop_Model_S|799.000000|
|19|PC_Model_S|799.000000|
|19|Printer_Model_S|299.000000|
|20|Laptop_Model_T|1499.500000|
|20|PC_Model_T|999.500000|
|20|Printer_Model_T|179.500000|
|21|Laptop_Model_U|899.990000|
|22|Laptop_Model_V|1199.000000|
|23|Laptop_Model_W|1399.500000|
|24|Laptop_Model_X|999.000000|
|25|Laptop_Model_Y|1199.750000|
|26|Laptop_Model_Z|1499.250000|


**Question 2:** What products are the top 5 most expensive based on their avg price ?

**Query:**
```sql
WITH CTE AS
(
SELECT 
DISTINCT code, 
model,
AVG(price) as avg_price
FROM PC
GROUP BY code, model
UNION 
SELECT 
DISTINCT code, 
model,
AVG(price) as avg_price
FROM Laptop
GROUP BY code, model
UNION
SELECT 
DISTINCT code, 
model,
AVG(price) as avg_price
FROM printer
GROUP BY code, model
)

SELECT TOP 5
model,
avg_price
FROM CTE 
ORDER BY avg_price DESC
```

**Result:**

|model|avg_price|
|---|---|
|Laptop_Model_L|1499.990000|
|PC_Model_R|1499.990000|
|Laptop_Model_H|1499.500000|
|Laptop_Model_T|1499.500000|
|Laptop_Model_Z|1499.250000|
