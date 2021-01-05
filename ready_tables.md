### Student

```
CREATE TABLE student
(
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    gender VARCHAR(50) NOT NULL,
    age INT NOT NULL,
    total_score INT NOT NULL 
 );
 
 INSERT INTO student 

VALUES (1, 'Jolly', 'Female', 20, 500), 
(2, 'Jon', 'Male', 22, 545), 
(3, 'Sara', 'Female', 25, 600), 
(4, 'Laura', 'Female', 18, 400), 
(5, 'Alan', 'Male', 20, 500), 
(6, 'Kate', 'Female', 22, 500), 
(7, 'Joseph', 'Male', 18, 643), 
(8, 'Mice', 'Male', 23, 543), 
(9, 'Wise', 'Male', 21, 499), 
(10, 'Elis', 'Female', 27, 400);
```

Find:
```
(1) Cumulative score by gender
(2) Rolling average of 3 people
(3) Info of a student having minimum marks by gender. If more than 1 than student with minimum id.
```

Answers:
(2)
```
SELECT S1.id, S1.name, S1.gender, S1.total_score, AVG(S2.total_score) 
FROM student S1, student S2
WHERE S1.id - S2.id BETWEEN 0 AND 2
GROUP BY S1.id
```

OR

```
SET @laglag := 0, @lag := 0;

SELECT id, name, gender, age, total_score,
   (total_score + lagscore+ lagscore)/3 AS MovingAvg
FROM
(SELECT *, @lag AS lagscore, @laglag AS laglagscore, 
    @laglag := @lag, @lag:=total_score   
FROM student S1) AS T
```


(3)
```
SELECT id, name, S.gender, total_score
FROM student AS S
WHERE S.id = (SELECT id
              FROM student WHERE gender = S.gender
              ORDER BY total_score, id 
              LIMIT 1)
```


### Sample

```
Create Table tab (
id INT PRIMARY KEY,
val INT,
type INT,
iditem INT
);

Insert into tab Values(1, 100, 1, 10);
Insert into tab Values(2, 150, 2, 10);
Insert into tab Values(3, 101, 1, 11);
Insert into tab Values(4, 90, 2, 11);
```
