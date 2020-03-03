#### Department Highest Salary

```
SELECT t.Department, E2.Name as Employee, E2.Salary FROM
Employee as E2 JOIN
(SELECT D.Id, D.Name as Department, MAX(Salary) as Salary
FROM Employee as E
JOIN Department as D
ON E.DepartmentId = D.Id
GROUP BY D.Id) as t
ON E2.DepartmentId = t.Id AND E2.Salary = t.Salary
```
