**(1) There are Two tables**
- attendances : date | student_id | attendance
- students : student_id | school_id | grade_level | date_of_birth | hometown

Using this data, could you answer questions like the following:

- What percent of students attend school on their birthday?
```
SELECT 100*COUNT(attendance)/(SELECT COUNT(*) FROM students) 
                         FROM attendances AS A
                         JOIN students AS S
                         ON A.date = S.date_of_birth AND A.student_id = S.student_id
                         WHERE attendance == "yes"
```
<br/>

- Which grade level had the largest drop in attendance between yesterday and today?
```
SELECT grade_level, SUM(CASE WHEN A.date = DATE('now') THEN 1 ELSE -1 END) AS attendance_drop
         FROM attendances AS A
         INNER JOIN students AS S
         ON A.student_id = S.student_id 
         WHERE A.attendance = "yes" AND A.date IN (date('now'), date('now', '-1 day'))
         GROUP BY grade_level
         ORDER BY attendance_drop DESC
         LIMIT 1
```

<br/>

**(2) I have two tables. Like this.**

extrafieldvalues:
| id | value | type | idItem |
-----|-------|------|--------|
| 1  | 100   | 1    | 10     |
| 2  | 150   | 2    | 10     |
| 3  | 101   | 1    | 11     |
| 4  | 90    | 2    | 11     |

items:
| id  | name |
------|-----
| 10  | foo  |
| 11  | bar  |


I need to make a query and get something like this:
| idItem  | valtype1 | valtype2 | name |
----------|----------|----------|-----
| 10      | 100      | 150      | foo  |
| 11      | 101      | 90       | bar  |

<br/>

```
SELECT idItem, MAX(case when type=1 then value end) as valtype1,
               MAX(case when type=2 then value end) as valtype2,
               name
FROM extrafieldvalues AS A JOIN items AS B 
                           ON A.idItem=B.id
GROUP BY idItem, name;
```
<br/>

**(3) Finding a median**
```
SET @rowindex := -1;
 
SELECT
   AVG(g.grade)
FROM
   (SELECT @rowindex:=@rowindex + 1 AS rowindex,
           grades.grade AS grade
    FROM grades
    ORDER BY grades.grade) AS g
WHERE
g.rowindex IN (FLOOR(@rowindex / 2) , CEIL(@rowindex / 2));
```
<br/>

**(4) Histogram**
```
select floor(mycol/10)*10 as bin_floor, count(*)
from mytable
group by bin_floor
order by bin_floor
```

**(5) Ranking**
Table: total_sales
|Name	|Sales|
------|------
John	|10
Jennifer	|15
Stella	|20
Sophia	|40
Greg	|50
Jeff	|20

```
SET @rowindex = 0;
 
SELECT Name, Sales, @rowindex:=@rowindex + 1 AS rank
FROM total_sales
ORDER BY Sales DESC, Name
```
or
```
SELECT A1.Name, A1.Sales, COUNT(A2.Sales) AS Rank
FROM total_sales AS A1, total_sales AS A2
WHERE A1.Sales < A2.Sales OR A1.Sales = A2.Sales
GROUP BY A1.Name, A1.Sales
ORDER BY A1.Sales DESC, A1.Name;
```

**(6) I have a table like this:**
```
Id  min_val  max_val
1   5        7
2   8        12
3   4        6
```
**I want to get True/False if the min_val and max_val are overlapping with any other ID. So, result like below:**
```
Id result
1  True  
2  False 
3  True
```
<br/>

- Answer

```
SELECT t2.id, 

CASE WHEN t2.id IN
(SELECT DISTINCT t.id
FROM tab1 as t
LEFT JOIN tab1 AS t1
WHERE t.id <> t1.id AND (t.min_val < t1.max_val AND t.max_val > t.min_val)) 
THEN "True" ELSE "False" END AS Results

FROM tab1 as t2
```

**(7) Match Win lose**

Table:
```
Create Table Match_Result (
Team_1 Varchar(20),
Team_2 Varchar(20),
Result Varchar(20)
);

Insert into Match_Result Values('India', 'Australia','India');
Insert into Match_Result Values('India', 'England','England');
Insert into Match_Result Values('SouthAfrica', 'India','India');
Insert into Match_Result Values('Australia', 'England',NULL);
Insert into Match_Result Values('England', 'SouthAfrica','SouthAfrica');
Insert into Match_Result Values('Australia', 'India','Australia');
```

Find team, total matches played by team, total wins, loses and ties.

```
SELECT T2.Team, 
	T2.Match_Played, 
	SUM(CASE WHEN T2.Team = M.Result THEN 1 ELSE 0 END) AS Match_Won,
	SUM(CASE WHEN T2.Team != M.Result THEN 1 ELSE 0 END) AS Match_Lost,
	SUM(CASE WHEN M.Result IS NULL THEN 1 ELSE 0 END) AS Match_Tied
FROM
(SELECT Team, COUNT(*) AS Match_Played FROM 
(SELECT Team_1 AS Team FROM Match_Result
UNION ALL
SELECT Team_2 AS Team FROM Match_Result) AS T
GROUP BY Team) AS T2, Match_Result AS M
WHERE T2.Team = M.Team_1 OR T2.Team = M.Team_2
GROUP BY T2.Team, T2.Match_Played;
```

**(8) Find Consecutive Numbers**

```
Create Table tab (
id INT PRIMARY KEY,
val INT
);

Insert into tab Values(1, 1);
Insert into tab Values(2, 1);
Insert into tab Values(3, 2);
Insert into tab Values(4, 3);
Insert into tab Values(5, 3);
Insert into tab Values(6, 3);
Insert into tab Values(7, 1);
Insert into tab Values(8, 1);
Insert into tab Values(9, 2);
```

Desired Result:
```
val consecutive_times
1   2
2   1
3   3
1   2
2   1
```

Answer:
```
SET @r1 =1, @r2 = 1;
SELECT MIN(val), COUNT(*)
FROM 
(SELECT id, val,
  IF(@r2 != val, @r1:=@r1+1 , @r1) AS r1, 
  IF(@r2 != val, @r2:=val , @r2) AS r2 
FROM tab) AS T
GROUP BY r1
```
