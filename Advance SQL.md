source: studybyyourself advanced sql

## Recursive Association
- joining between a table and that table itself, which is known under self-join.

(1) Top Level:

```
SELECT First_name, Last_name
FROM Worker
WHERE Boss_id IS NULL
```

(2) First Level:

```
SELECT W2.First_name, W2.Last_name, W1.Last_name AS DirectBoss
FROM Worker AS W1, Worker AS W2
WHERE W2.Boss_id = W1.ID
      AND W1.First_name = 'Eddy' AND W1.Last_name ='Fisher'
```

(3) Second Level:

```
SELECT W3.First_name, W3.Last_name, W2.Last_name AS DirectBoss
FROM Worker AS W1, Worker AS W2, Worker AS W3
WHERE W3.Boss_id = W2.ID
      AND W2.Boss_id = W1.ID
      AND W1.First_name = 'Eddy' AND W1.Last_name ='Fisher' 
```

## Derived table

```
SELECT P.ID, P.post_title, IFNULL(derivLikes.nbLikes,0) AS Likes, IFNULL(derivComm.nbComments,0) AS Comments
FROM Posts AS P
LEFT JOIN
   ( SELECT post_ID, COUNT(*) AS nbLikes
     FROM Likes
     GROUP BY post_ID ) AS derivLikes ON P.ID = derivLikes.post_ID
LEFT JOIN 
   ( SELECT post_ID, COUNT(*) AS nbComments
     FROM Comments
     GROUP BY post_ID ) AS derivComm ON P.ID = derivComm.post_ID
```

## Top n rows per group

```
(SELECT * FROM Employee WHERE Department_id = 1 ORDER BY Salary DESC LIMIT 0,2)
UNION
(SELECT * FROM Employee WHERE Department_id = 2 ORDER BY Salary DESC LIMIT 0,2)
UNION
(SELECT * FROM Employee WHERE Department_id = 3 ORDER BY Salary DESC LIMIT 0,2)
UNION
(SELECT * FROM Employee WHERE Department_id = 4 ORDER BY Salary DESC LIMIT 0,2)
```

## Optimization

(1) Correlated Subquery
- A subquery is said to be correlated when the subquery relates to the main query.

**For each department, the data about the employee having started to work the first in (most senior):**
```
SELECT dep.Name AS Department, emp.First_name, emp.Last_name, emp.Start
FROM Employee AS emp, Department AS dep
WHERE emp.Department_ID = dep.ID AND emp.Start = (
	SELECT MIN(e.Start)
	FROM Employee AS e
	WHERE e.Department_ID = emp.Department_ID
)
```

(2) Optimization
- Instead of computing the minimal start date for a given department at each iteration in the WHERE clause, let’s compute first all minimal start dates per department and store them in a derived table. Then let’s perform a self-join for filtering by minimal start date.

- The trick lies in the use of both Start and Department_id as keys for doing the self-join.

```
SELECT D.Name AS Department, e.First_name, e.Last_name, e.Start
FROM (
    SELECT e.Department_id AS Dep_id, MIN(e.Start) AS Start
    FROM Employee AS e
    GROUP BY e.Department_id
    ) AS Min_start_by_dep, Employee AS e, Department AS D
WHERE Min_start_by_dep.Start = e.Start AND e.Department_id = Min_start_by_dep.Dep_id AND e.Department_id = D.ID
```


## Case Functions

```
SELECT 
  CASE WHEN day_of_week in ('Sat', 'Sun') 
    then 'Weekend' else 'Weekday' end as day_type
FROM table_a;
```

```
SELECT 
  SUM(
    CASE WHEN day_of_week in ('Sat', 'Sun')
    THEN 1 ELSE 0 END) as num_weekend_days
FROM table_a;
```
