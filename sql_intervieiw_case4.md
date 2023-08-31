You are given two tables that look like this - 

### Hotel table - 

|Hotel_ID|Hotel_name|location|
|---|---|---|
|1|Hotel_A|City_A|
|2|Hotel_B|City_B|
|3|Hotel_C|City_C|
|1|Hotel_A|City_A|
|2|Hotel_B|City_B|
|3|Hotel_C|City_C|
|1|Hotel_A|City_A|
|2|Hotel_B|City_B|
|3|Hotel_C|City_C| 

### Rooms table - 

|Hotel_ID|Room_no|type|rent|
|---|---|---|---|
|1|101|Single|100|
|1|102|Double|NULL|
|1|103|Suite|250|
|2|201|Single|120|
|2|202|Double|NULL|
|2|203|Suite|280|
|3|301|Single|110|
|3|302|Double|160|
|3|303|Suite|260|

**Question:** List all the hotel rooms and their rent from New York. Besides that, if rent is NULL, in place of price, replace it with the text “Not Available.”<br>

**Query:**

```sql
SELECT 
DISTINCT r.Hotel_ID,
r.Room_no, 
COALESCE (CAST(r.rent AS VARCHAR), 'Not available') AS rent     -- We are using CAST to change the data type of the column from 'INT' to 'VARCHAR' to make it compatible with the data type of 'Not available' 
-- CASE WHEN r.rent IS NULL THEN 'Not available' ELSE CAST(r.rent AS VARCHAR) END AS rent  -- same result can also be achieved using CASE statement
FROM Rooms r, Hotel h
WHERE h.[location] = 'City_B' 
AND h.Hotel_ID = r.Hotel_ID
```

**Result:**

|Hotel_ID|Room_no|rent|
|---|---|---|
|2|201|120|
|2|202|Not available|
|2|203|280|
