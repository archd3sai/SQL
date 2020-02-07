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
SELECT grade_level, SUM(CASE WHEN A.date = DATE('now') THEN 1 ELSE -1) AS attendance_drop
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
