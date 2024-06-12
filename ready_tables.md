## Tables and Questions

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
(1) Cumulative score by gender ordered by id
(2) Rolling average of 3 people
(3) Info of a student having minimum marks by gender. If more than 1 than student with minimum id.
```

Answers:
(1)
```
SELECT *, SUM(total_score) OVER(PARTITION BY gender ORDER BY id) as cumulative_score
FROM student
ORDER BY id
```

(2)
```
SELECT S1.id, S1.name, S1.gender, S1.total_score, AVG(S2.total_score) 
FROM student S1, student S2
WHERE S1.id - S2.id BETWEEN 0 AND 2
GROUP BY S1.id
```

OR

```
SELECT *, AVG(total_score) OVER(ORDER BY ID ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) as moving_avg
FROM student
ORDER BY id
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
OR
```
WITH min_score_tab as (
SELECT id, name, total_score, 
RANK() OVER(PARTITION BY gender order by total_score, id) as rnk
FROM student)
select id, name, total_score from min_score_tab
where rnk = 1;
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
2. How to find the maximum length of an uninterrupted 0 value sequence?
3. How many times does the sensor switch between the two?
```

Answers: (1)
```
WITH calculated AS (
    SELECT 
        TS, 
        VAL, 
        LAG(VAL) OVER (ORDER BY TS) AS lag_val,
        LAG(TS) OVER (ORDER BY TS) AS lag_ts,
        LEAD(VAL) OVER (ORDER BY TS) AS lead_val,
        LEAD(TS) OVER (ORDER BY TS) AS lead_ts
    FROM 
        seq_data
),
downtime_raw AS (
    SELECT 
        * 
    FROM 
        calculated 
    WHERE 
        NOT (VAL = 0 AND lag_val = 0) OR (lag_val IS NULL)
),
downtime_events AS (
    SELECT 
        TS, 
        VAL, 
        EXTRACT(EPOCH FROM TS) - EXTRACT(EPOCH FROM lag_ts) AS time_diff 
    FROM 
        downtime_raw
    WHERE 
        VAL = 1 AND lag_val = 0
)
SELECT 
    AVG(time_diff) AS avg_downtime, 
    COUNT(time_diff) AS total_downtime_events, 
    SUM(time_diff) AS total_downtime 
FROM 
    downtime_events;

```

(2)
```
WITH grp_sequences AS (
    SELECT
        TS,
        VAL,
        ROW_NUMBER() OVER (ORDER BY TS) - ROW_NUMBER() OVER (PARTITION BY VAL ORDER BY TS) AS grp
    FROM
        seq_data
) 
SELECT COUNT(*) AS zero_seq
FROM grp_sequences
WHERE VAL = 0
GROUP BY grp
ORDER BY zero_seq DESC
LIMIT 1
```


Below makes group until value changes
```
 SELECT
        TS,
        VAL, ROW_NUMBER() OVER (ORDER BY TS) as a,
        ROW_NUMBER() OVER (PARTITION BY VAL ORDER BY TS) as b, 
        ROW_NUMBER() OVER (ORDER BY TS) - ROW_NUMBER() OVER (PARTITION BY VAL ORDER BY TS) AS grp
    FROM
        seq_data
```

(3)
```
SELECT
    COUNT(*) AS switch_count
FROM
    (
        SELECT
            TS,
            VAL,
            LAG(VAL) OVER (ORDER BY TS) AS prev_val
        FROM
            seq_data
    ) AS switch_events
WHERE
    VAL <> prev_val;
```



## Sample data

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
