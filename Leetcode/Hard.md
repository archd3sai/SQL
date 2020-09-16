
### Question: Human Traffic of Stadium [Link](https://leetcode.com/problems/human-traffic-of-stadium/)
```
select id, visit_date, people from stadium where people >=100 and
(
(
id+1 in (select id from stadium where people>=100) and
id+2 in (select id from stadium where people>=100)
)
or
(
id-1 in (select id from stadium where people>=100) and
id+1 in (select id from stadium where people>=100)
)
or
(
id-1 in (select id from stadium where people>=100) and
id-2 in (select id from stadium where people>=100)
)
);
```

### Question: Department Top 3 Salaries [Link](https://leetcode.com/problems/department-top-three-salaries/)

Solution 1:
```
SELECT D.Name AS Department, E.Name AS Employee, E.Salary AS Salary 
FROM Employee E JOIN Department D ON E.DepartmentId = D.Id 

WHERE (SELECT COUNT(DISTINCT(Salary)) FROM Employee 
       WHERE DepartmentId = E.DepartmentId AND Salary > E.Salary) < 3

ORDER by E.DepartmentId, E.Salary DESC;
```
Solution 2:
```
# Write your MySQL query statement below
SELECT D.Name AS Department, E.Name AS Employee, E.Salary 
FROM Employee AS E
JOIN Department AS D ON E.DepartmentId = D.Id

WHERE E.Salary IN

(SELECT * FROM (SELECT DISTINCT E2.Salary 
FROM Employee AS E2
WHERE E2.DepartmentId = E.DepartmentId
ORDER BY E2.Salary DESC LIMIT 3) AS sq)
```
**Note:** Since Mysql does not allow a LIMIT clause in sub query. So, we override this limitation by wrapping our subquery inside another query. 

### Question: Trips and Users [Link](https://leetcode.com/problems/trips-and-users/)

```
SELECT T.Request_at AS Day, 
    ROUND(AVG(CASE WHEN T.Status like 'cancelled%' THEN 1 ELSE 0 END), 2) AS 'Cancellation Rate'
FROM Trips T
JOIN Users U ON T.Client_Id = U.Users_Id
WHERE U.Banned = 'No' AND T.Request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY T.Request_at
```

