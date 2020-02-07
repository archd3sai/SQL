(1) There are Two tables
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

- Which grade level had the largest drop in attendance between yesterday and today?
```
SELECT grade_level, COUNT(attendance) FROM attendances AS A
         LEFT JOIN students AS S
         ON A.student_id = S.student_id
         GROUP BY grade_level
         HAVING attendance == "yes"
         AND date == "02-06-2020"
```
