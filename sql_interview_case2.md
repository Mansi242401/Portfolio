
## Scenario

You have four tables - each table has information about the planets in our solar system. The tables have a similar structure but they come from different sources. The data is collected daily from satellites and hence different table may have same rows/duplicate records.Your task is to combine the data set into a single table with unique rows in that table.You can use UNION or UNION ALL.

**Question 1** : Which(UNION or UNIION ALL) you will select and why?<br>
**Ans** : Both Union and Union All are used to combine data with similar structure. However, UNION combines data without duplicating rows and UNION ALL combines all the data with duplicate rows included.In this case, requirement is to get the unique rows, hence we will pick UNION

### SQL Script to create Sample Data 
```sql
CREATE TABLE Initial_Table (
    planet CHAR(32),
    category CHAR(64),
    temp_in_C INT,
    Date DATE
)

CREATE TABLE History_Table (
    planet CHAR(32),
    category CHAR(64),
    temp_in_C INT,
    Date DATE
)


CREATE TABLE Terrestrial_Table (
    planet CHAR(32),
    category CHAR(64),
    temp_in_C INT,
    Date DATE,
    Size_in_million_km INT
)
 
 CREATE TABLE Outer_planet_Table (
    planet CHAR(32),
    category CHAR(64),
    temp_in_C INT,
    Size_in_millions_km INT,
    Date DATE
 )

 INSERT INTO Initial_Table 
 VALUES
 ('Mercury', 'Terrestrial Planet', 430, '2021-03-01'),
 ('Venus', 'Terrestrial Planet', 465, '2021-03-01'),
 ('Earth', 'Terrestrial Planet', 35, '2021-03-01'),
 ('Mars', 'Terrestrial Planet', 15, '2021-03-01'),
 ('Jupiter', 'Giant Planets', -50, '2021-03-01'),
 ('Saturn', 'Giant Planets', -134, '2021-03-01'),
 ('Uranus', 'Ice Planets', -168, '2021-03-01'),
 ('Neptune', 'Ice Planets', -360, '2021-03-01'),
 ('Mercury', 'Terrestrial Planet', 320, '2021-03-01'),
 ('Venus', 'Terrestrial Planet', 299, '2021-03-02'),
 ('Earth', 'Terrestrial Planet', 33, '2021-03-02'),
 ('Mars', 'Terrestrial Planet', 13, '2021-03-02')

 INSERT INTO History_Table
 VALUES
 ('Mars', 'Terrestrial Planet', 15, '2021-03-01'),
 ('Jupiter', 'Giant Planets', -50, '2021-03-01'),
 ('Saturn', 'Giant Planets', -134, '2021-03-01'),
 ('Uranus', 'Ice Planets', -168, '2021-03-01'),
 ('Neptune', 'Ice Planets', -360, '2021-03-01'),
 ('Mercury', 'Terrestrial Planet', 319, '2021-02-28'),
 ('Venus', 'Terrestrial Planet', 289, '2021-02-28'),
 ('Earth', 'Terrestrial Planet', 20, '2021-02-28'),
 ('Mars', 'Terrestrial Planet', 15, '2021-02-28'),
 ('Jupiter', 'Giant Planets', -60,  '2021-02-28'),
 ('Mars', 'Terrestrial Planet', 11, '2021-02-27'),
 ('Jupiter', 'Giant Planets', -40, '2021-02-27')
 
 INSERT INTO Terrestrial_Table 
 VALUES 
 ('Mercury', 'Terrestrial Planet', 430, '2021-03-01', 46),
 ('Venus', 'Terrestrial Planet', 466, '2021-03-01', 108),
 ('Earth', 'Terrestrial Planet', 35, '2021-03-01', 150),
 ('Mars', 'Terrestrial Planet', 15, '2021-03-01', 249)

INSERT INTO Outer_planet_Table
VALUES
('Jupiter', 'Giant Planets', -50, 817, '2021-03-01'),
('Saturn', 'Giant Planets', -134, 1350, '2021-03-01'),
('Uranus', 'Ice Planets', -168, 4550, '2021-03-01'),
('Neptune', 'Ice Planets', -360, 5910, '2021-03-01')
```

**Question 2** : Write a select statement to produce the above desired result. <br>
**Solution:**
```sql
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Initial_Table
UNION 
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM History_Table
UNION
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Terrestrial_Table
UNION
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Outer_planet_Table
```
**Result:**
|planet|category|temp_in_C|DATE|
|---|---|---|---|
|Earth                           |Terrestrial Planet                                              |20|2021-02-28|
|Earth                           |Terrestrial Planet                                              |33|2021-03-02|
|Earth                           |Terrestrial Planet                                              |35|2021-03-01|
|Jupiter                         |Giant Planets                                                   |-60|2021-02-28|
|Jupiter                         |Giant Planets                                                   |-50|2021-03-01|
|Jupiter                         |Giant Planets                                                   |-40|2021-02-27|
|Mars                            |Terrestrial Planet                                              |11|2021-02-27|
|Mars                            |Terrestrial Planet                                              |13|2021-03-02|
|Mars                            |Terrestrial Planet                                              |15|2021-02-28|
|Mars                            |Terrestrial Planet                                              |15|2021-03-01|
|Mercury                         |Terrestrial Planet                                              |319|2021-02-28|
|Mercury                         |Terrestrial Planet                                              |320|2021-03-01|
|Mercury                         |Terrestrial Planet                                              |430|2021-03-01|
|Neptune                         |Ice Planets                                                     |-360|2021-03-01|
|Saturn                          |Giant Planets                                                   |-134|2021-03-01|
|Uranus                          |Ice Planets                                                     |-168|2021-03-01|
|Venus                           |Terrestrial Planet                                              |289|2021-02-28|
|Venus                           |Terrestrial Planet                                              |299|2021-03-02|
|Venus                           |Terrestrial Planet                                              |465|2021-03-01|
|Venus                           |Terrestrial Planet                                              |466|2021-03-01|

**Question3:** Find the planets that have greater than 0 degree Celcius temperature <br>
**Solution:**
```sql
WITH CTE AS 
(
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Initial_Table
UNION 
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM History_Table
UNION
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Terrestrial_Table
UNION
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Outer_planet_Table
)
SELECT 
planet, 
Date, 
temp_in_C 
FROM CTE 
WHERE temp_in_C > 0
```

**Result:**
|planet|Date|temp_in_C|
|---|---|---|
|Earth                           |2021-02-28|20|
|Earth                           |2021-03-02|33|
|Earth                           |2021-03-01|35|
|Mars                            |2021-02-27|11|
|Mars                            |2021-03-02|13|
|Mars                            |2021-02-28|15|
|Mars                            |2021-03-01|15|
|Mercury                         |2021-02-28|319|
|Mercury                         |2021-03-01|320|
|Mercury                         |2021-03-01|430|
|Venus                           |2021-02-28|289|
|Venus                           |2021-03-02|299|
|Venus                           |2021-03-01|465|
|Venus                           |2021-03-01|466|

**Question4:** Find the planets that start with a 'M' or a 'N' <br>
**Solution:**
```sql
WITH CTE AS 
(
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Initial_Table
UNION 
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM History_Table
UNION
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Terrestrial_Table
UNION
SELECT 
planet,
category,
temp_in_C,
DATE 
FROM Outer_planet_Table
)
SELECT 
DISTINCT planet
FROM CTE 
WHERE planet LIKE 'M%' 
OR planet LIKE 'N%'
```

**Result:**
|planet|
|---|
|Mars                            |
|Mercury                         |
|Neptune                         |

