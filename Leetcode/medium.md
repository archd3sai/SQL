### Nth Highest Salary

```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
      SELECT Salary FROM Employee e1 
      WHERE N-1 = (SELECT COUNT(DISTINCT Salary) FROM Employee e2 
                   WHERE e2.salary > e1.salary)

  );
END
```
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
      SELECT DISTINCT Salary FROM Employee 
      ORDER BY Salary DESC LIMIT N-1, 1
  );
END
```


### Department Highest Salary

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

### Consecutive Numbers
```
SELECT DISTINCT l1.Num As ConsecutiveNums
FROM Logs l1, Logs l2, Logs l3
WHERE l1.Num = l2.Num 
    AND l2.Num = l3.Num 
    AND l1.Id = l2.Id + 1 
    AND l2.Id = l3.Id + 1;
```

### Exchange Seats
```
SELECT IF(cnt % 2 = 1 AND id = cnt, id, IF(id % 2 = 1, id + 1, id - 1)) AS id, student FROM seat,
(SELECT COUNT(*) AS cnt FROM seat) AS t
ORDER BY id;
```

### Rank Scores
```
SELECT s.Score, COUNT(t.Score) AS Rank FROM
(SELECT DISTINCT Score FROM Scores) as t, Scores as s
WHERE s.Score<=t.Score
GROUP BY s.Id, s.Score
ORDER BY Rank
```
