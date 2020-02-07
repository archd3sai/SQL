**(1) There are Two tables**
- attendances : date | student_id | attendance
- students : student_id | school_id | grade_level | date_of_birth | hometown

Using this data, could you answer questions like the following:

- What percent of students attend school on their birthday?
```
SELECT COUNT(attendance)/(SELECT COUNT(*) FROM students) 
                         FROM attendances AS A
                         RIGHT JOIN students AS S
                         ON A.date = S.date_of_birth 
                         AND A.student_id = S.student_id
                         WHERE attendance == "yes"
```
<br/>

- Which grade level had the largest drop in attendance between yesterday and today?
```
SELECT grade_level, COUNT(attendance) FROM attendances AS A
         LEFT JOIN students AS S
         ON A.student_id = S.student_id
         GROUP BY grade_level
         HAVING attendance == "yes"
         AND date == "02-06-2020"
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
FROM extrafieldvalues a INNER JOIN items b on a.idItem=b.id
GROUP BY idItem,name
```
