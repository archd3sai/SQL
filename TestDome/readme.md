
**(1) A table containing the students enrolled in a yearly course has incorrect data in records with ids between 20 and 100 (inclusive). Write a query that updates the field 'year' of every faulty record to 2015.**

```
UPDATE enrollments SET year = 2015 WHERE id BETWEEN 19 AND 101;
```
<br/>

**(2) Information about pets is kept in two separate tables. Write a query that select all distinct pet names.**

```
SELECT DISTINCT name FROM (SELECT name FROM dogs
                           UNION
                           SELECT name FROM cats);
```
<br/>

**(3) Write a query that selects userId and average session duration for each user who has more than one session.**

```
SELECT userId, AVG(duration) FROM sessions GROUP BY userId HAVING count(id) > 1;
```
<br/>

**(4) Each item in a web shop belongs to a seller. To ensure service quality, each seller has a rating. The data are kept in the following two tables: sellers and items. Write a query that selects the item name and the name of its seller for each item that belongs to a seller with a rating greater than 4. The query should return the name of the item as the first column and name of the seller as the second column.**

```
SELECT I.name, S.name FROM items as I, sellers as S WHERE I.sellerId == S.id AND S.rating > 4
```
<br/>

**(5) An insurance company maintains records of sales made by its employees. Each employee is assigned to a state. States are grouped under regions. The following tables contain the data:**
```
TABLE regions
  id INTEGER PRIMARY KEY
  name VARCHAR(50) NOT NULL

TABLE states
  id INTEGER PRIMARY KEY
  name VARCHAR(50) NOT NULL
  regionId INTEGER NOT NULL REFERENCES regions(id)

TABLE employees
  id INTEGER PRIMARY KEY
  name VARCHAR(50) NOT NULL
  stateId INTEGER NOT NULL REFERENCES states(id)

TABLE sales
  id INTEGER PRIMARY KEY
  amount INTEGER NOT NULL
  employeeId INTEGER NOT NULL REFERENCES employees(id)  
Management requires a comparative region sales analysis report.
```
**Write a query that returns:**

**(1) The region name, (2) Average sales per employee for the region (Average sales = Total sales made for the region / Number of employees in the region), (3) The difference between the average sales of the region with the highest average sales, and the average sales per employee for the region (average sales to be calculated as explained above).**

**A region with no sales should be also returned. Use 0 for average sales per employee for such a region when calculating the 2nd and the 3rd column.**

```
SELECT region, Avg_Sales, MAX(Avg_Sales) OVER() - Avg_Sales as Diff_Sales FROM    
    (SELECT r.name AS region, IFNULL(SUM(sl.amount)/COUNT(DISTINCT(e.id)),0) AS Avg_Sales FROM regions AS r
    LEFT JOIN states as s ON r.id = s.regionId
    LEFT JOIN employees as e ON s.id = e.stateId
    LEFT JOIN sales as sl ON e.id = sl.employeeId
    GROUP BY r.id) AS T
```