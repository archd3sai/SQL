### (1) Student Score

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

### (2) Sensor Data

Given sensor data as below VAL shows if a machine is up or down. When a value is received, it shows condition of a machine at that time. For example, machine was came up at 2021-01-01 00:41:20 and it went down on 2021-01-01 00:41:55. 

```
Create Table seq_data (
TS datetime,
VAL INT
);

Insert into seq_data Values('2021-01-01 00:40:00', 0);
Insert into seq_data Values('2021-01-01 00:41:20', 1);
Insert into seq_data Values('2021-01-01 00:41:26', 1);
Insert into seq_data Values('2021-01-01 00:41:55', 0);
Insert into seq_data Values('2021-01-01 00:42:20', 1);
Insert into seq_data Values('2021-01-01 00:42:32', 0);
Insert into seq_data Values('2021-01-01 00:42:45', 0);
Insert into seq_data Values('2021-01-01 00:44:10', 0);
Insert into seq_data Values('2021-01-01 00:44:12', 1);
Insert into seq_data Values('2021-01-01 00:45:46', 1);
Insert into seq_data Values('2021-01-01 00:46:05', 0);
```

Find:
```
1. What is the total downtime, average downtime and longest downtime of the device?
2. How to find the maximum length of an uninterrupted 1 value sequence?
3. How many times does the sensor switch between the two?
```

Answers: (1)
```
SELECT SUM(downtime) AS total_downtime, 
AVG(downtime) AS average_downtime,
MAX(downtime) maximum_downtime, 
COUNT(downtime) number_of_downtimes from
(SELECT end_time - MIN(start_time) AS downtime from
(SELECT s1.TS AS start_time, MIN(s2.TS) AS end_time
FROM seq_data s1, seq_data s2
WHERE s2.TS > s1.TS
AND s1.VAL = 0
AND s2.VAL = 1
GROUP BY s1.TS) T
GROUP BY end_time) T2;

```

### Sample data

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
