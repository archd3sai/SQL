## Easy
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

## Hard
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

**The region name, Average sales per employee for the region (Average sales = Total sales made for the region / Number of employees in the region), The difference between the average sales of the region with the highest average sales, and the average sales per employee for the region (average sales to be calculated as explained above).**

**A region with no sales should be also returned. Use 0 for average sales per employee for such a region when calculating the 2nd and the 3rd column.**

```
SELECT region, Avg_Sales, MAX(Avg_Sales) OVER() - Avg_Sales as Diff_Sales FROM    
    (SELECT r.name AS region, IFNULL(SUM(sl.amount)/COUNT(DISTINCT(e.id)),0) AS Avg_Sales FROM regions AS r
    LEFT JOIN states as s ON r.id = s.regionId
    LEFT JOIN employees as e ON s.id = e.stateId
    LEFT JOIN sales as sl ON e.id = sl.employeeId
    GROUP BY r.id) AS T
```
<br/>

**(6) The following data definition defines an organization's employee hierarchy. An employee is a manager if any other employee has their managerId set to the first employees id. An employee who is a manager may or may not also have a manager.**
```
TABLE employees
  id INTEGER NOT NULL PRIMARY KEY
  managerId INTEGER REFERENCES employees(id)
  name VARCHAR(30) NOT NULL
```
**Write a query that selects the names of employees who are not managers.**
```
SELECT name FROM employees WHERE id NOT IN 
    (SELECT managerId FROM employees WHERE managerId IS NOT NULL)
```
<br/>

**(7) The following two tables are used to define users and their respective roles:**
```
TABLE users
  id INTEGER NOT NULL PRIMARY KEY,
  userName VARCHAR(50) NOT NULL

TABLE roles
  id INTEGER NOT NULL PRIMARY KEY,
  role VARCHAR(20) NOT NULL
```
**The users_roles table should contain the mapping between each user and their roles. Each user can have many roles, and each role can have many users.**

**Modify the provided SQLite create table statement so that: Only users from the users table can exist within users_roles, Only roles from the roles table can exist within users_roles, A user can only have a specific role once.**

```
CREATE TABLE users_roles (
  userId INTEGER NOT NULL,
  roleId INTEGER NOT NULL,
  PRIMARY KEY (userId, roleId),
  FOREIGN KEY (userId) REFERENCES users(id),
  FOREIGN KEY (roleId) REFERENCES roles(id)
);
```
## Notes
- **MAX(Avg_Sales) OVER()** finds the maximum of the columns and creates a column with that maximum value.
- A **NOT IN** query will not return any rows if any NULLs exists in the list of NOT IN values. (For question no. 6)
