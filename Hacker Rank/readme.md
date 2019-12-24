## Hacker Rank Challenges that I have solved:

**(1) Query the list of CITY names from STATION that do not start with vowels. Your result cannot contain duplicates.**

```
SELECT DISTINCT CITY FROM STATION WHERE upper(SUBSTR(CITY,1,1)) NOT IN ('A','E','I','O','U');
```

**(2) Query the list of CITY names from STATION which have vowels (i.e., a, e, i, o, and u) as both their first and last characters. Your result cannot contain duplicates.**

```
SELECT CITY FROM STATION WHERE UPPER(SUBSTR(CITY,1,1)) IN ('A','E','I','O','U') AND UPPER(SUBSTR(CITY,-1,1)) IN ('A','E','I','O','U');
```

**(3) find the difference between the total number of CITY entries in the table and the number of distinct CITY entries in the table.**

```
SELECT COUNT(*) - COUNT(DISTINCT(CITY)) FROM STATION;
```

**(4) Write a query identifying the type of each record in the TRIANGLES table using its three side lengths.**

```
SELECT IF(A+B>C AND A+C>B AND B+C>A, IF(A=B AND B=C, 'Equilateral', IF(A=B OR B=C OR A=C, 'Isosceles', 'Scalene')), 'Not A Triangle') FROM TRIANGLES;
```

**(5) Query the greatest value of the Northern Latitudes (LAT_N) from STATION that is less than 137.2345. Truncate your answer to 4 decimal places.**

```
SELECT ROUND(MAX(LAT_N), 4) FROM STATION WHERE LAT_N < 137.2345;
```

**(6) Query the Western Longitude (LONG_W) for the largest Northern Latitude (LAT_N) in STATION that is less than 137.2345. Round your answer to 4 decimal places.**

```
SELECT ROUND(LONG_W, 4) FROM STATION WHERE LAT_N < 137.2345 ORDER BY LAT_N DESC LIMIT 1;
```

**(7) Given the CITY and COUNTRY tables, query the names of all cities where the CONTINENT is 'Africa'.**

```
SELECT CI.NAME FROM CITY AS CI JOIN COUNTRY AS CO ON CO.CODE = CI.COUNTRYCODE WHERE CO.CONTINENT = 'Africa';
```

**(8) Given the CITY and COUNTRY tables, query the names of all the continents (COUNTRY.Continent) and their respective average city populations (CITY.Population) rounded down to the nearest integer.**

```
SELECT CO.CONTINENT, FLOOR(AVG(CI.POPULATION)) FROM CITY AS CI JOIN COUNTRY AS CO ON CO.CODE = CI.COUNTRYCODE GROUP BY CO.CONTINENT;
```

**(9) You are given a table, BST, containing two columns: N and P, where N represents the value of a node in Binary Tree, and P is the parent of N. Write a query to find the node type of Binary Tree ordered by the value of the node (root, leaf, inner).**

```
SELECT N, IF(P IS NULL, 'Root', IF(B.N IN (SELECT P FROM BST), 'Inner', 'Leaf')) FROM BST AS B ORDER BY N;
```

**(10) Query an alphabetically ordered list of all names in OCCUPATIONS, immediately followed by the first letter of each profession as a parenthetical (i.e.: enclosed in parentheses). For example: AnActorName(A), ADoctorName(D), AProfessorName(P), and ASingerName(S). Query the number of ocurrences of each occupation in OCCUPATIONS. Sort the occurrences in ascending order.**

```
SELECT CONCAT(Name, '(' ,SUBSTR(Occupation,1,1), ')') FROM OCCUPATIONS ORDER BY Name;
SELECT CONCAT('There are a total of ', COUNT(*), ' ', LOWER(Occupation), 's.') FROM OCCUPATIONS GROUP BY Occupation ORDER BY COUNT(Occupation), Occupation; 
```

**(11) Pivot the Occupation column in OCCUPATIONS so that each Name is sorted alphabetically and displayed underneath its corresponding Occupation. The output column headers should be Doctor, Professor, Singer, and Actor, respectively.
Note: Print NULL when there are no more names corresponding to an occupation.**

```
SET @r1=0, @r2=0, @r3 =0, @r4=0;

SELECT MIN(Doctor), MIN(Professor), MIN(Singer), MIN(Actor) FROM
(SELECT CASE Occupation WHEN 'Doctor' THEN @r1:=@r1+1
                       WHEN 'Professor' THEN @r2:=@r2+1
                       WHEN 'Singer' THEN @r3:=@r3+1
                       WHEN 'Actor' THEN @r4:=@r4+1 END
       AS RowLine,
       CASE WHEN Occupation = 'Doctor' THEN Name END AS Doctor,
       CASE WHEN Occupation = 'Professor' THEN Name END AS Professor,
       CASE WHEN Occupation = 'Singer' THEN Name END AS Singer,
       CASE WHEN Occupation = 'Actor' THEN Name END AS Actor
       FROM OCCUPATIONS ORDER BY Name) AS t
GROUP BY RowLine;
```

**(12) Euclidean Distance**

```
SELECT ROUND(SQRT(POW(MIN(LAT_N)-MAX(LAT_N), 2) + POW(MIN(LONG_W)-MAX(LONG_W), 2)), 4) FROM STATION;
```

**(13) We define an employee's total earnings to be their monthly (salary X months) worked, and the maximum total earnings to be the maximum total earnings for any employee in the Employee table. Write a query to find the maximum total earnings for all employees as well as the total number of employees who have maximum total earnings. Then print these values as 2 space-separated integers.**

```
SELECT (months*salary) as earnings, COUNT(*) FROM Employee GROUP BY earnings ORDER BY earnings DESC LIMIT 1;
```

**(14) A median is defined as a number separating the higher half of a data set from the lower half. Query the median of the Northern Latitudes (LAT_N) from STATION and round your answer to 4 decimal places.**

```
SET @r1 = -1;

SELECT ROUND(AVG(t.LAT_N),4) FROM
(
SELECT @r1:=@r1+1 as rowsN, s.LAT_N as lat_n FROM STATION AS s ORDER BY s.LAT_N
) AS t WHERE t.rowsN IN (FLOOR(@r1/2), CEIL(@r1/2))
```

**(15) Given the table schemas below, write a query to print the company_code, founder name, total number of lead managers, total number of senior managers, total number of managers, and total number of employees. Order your output by ascending company_code.**

**Note: The tables may contain duplicate records.**
**- The company_code is string, so the sorting should not be numeric. For example, if the company_codes are C_1, C_2, and C_10, then the ascending company_codes will be C_1, C_10, and C_2.**

```
SELECT c.company_code, c.founder, 
       COUNT(DISTINCT l.lead_manager_code), COUNT(DISTINCT s.senior_manager_code),
       COUNT(DISTINCT m.manager_code), COUNT(DISTINCT e.employee_code)
FROM Company c, Lead_Manager l, Senior_Manager s, Manager m, Employee e
WHERE c.company_code = l.company_code AND 
      l.lead_manager_code = s.lead_manager_code AND
      s.senior_manager_code = m.senior_manager_code AND
      m.manager_code = e.manager_code
GROUP BY c.company_code, c.founder ORDER BY c.company_code;
```

**(16) Julia just finished conducting a coding contest, and she needs your help assembling the leaderboard! Write a query to print the respective hacker_id and name of hackers who achieved full scores for more than one challenge. Order your output in descending order by the total number of challenges in which the hacker earned a full score. If more than one hacker received full scores in same number of challenges, then sort them by ascending hacker_id.**

```
SELECT H.hacker_id, H.name 
    FROM Hackers AS H 
    JOIN Submissions AS S ON H.hacker_id = S.hacker_id
    JOIN Challenges AS C ON S.challenge_id = C.challenge_id
    JOIN Difficulty AS D ON C.difficulty_level = D.difficulty_level
    WHERE S.score = D.score
    GROUP BY H.hacker_id, H.name HAVING COUNT(*) > 1 ORDER BY COUNT(*) DESC, h.hacker_id;
```

**(17) Ketty gives Eve a task to generate a report containing three columns: Name, Grade and Mark. Ketty doesn't want the NAMES of those students who received a grade lower than 8. The report must be in descending order by grade -- i.e. higher grades are entered first. If there is more than one student with the same grade (8-10) assigned to them, order those particular students by their name alphabetically. Finally, if the grade is lower than 8, use "NULL" as their name and list them by their grades in descending order. If there is more than one student with the same grade (1-7) assigned to them, order those particular students by their marks in ascending order.**

```
SELECT IF(G.Grade<8, NULL, S.Name), G.Grade, S.Marks 
    FROM Students AS S
    JOIN Grades AS G ON S.Marks BETWEEN G.Min_Mark AND G.Max_Mark
    ORDER BY G.Grade DESC, IF(G.Grade<8, S.Marks, S.Name);
```    

**(18) Hermione decides the best way to choose is by determining the minimum number of gold galleons needed to buy each non-evil wand of high power and age. Write a query to print the id, age, coins_needed, and power of the wands that Ron's interested in, sorted in order of descending power. If more than one wand has same power, sort the result in order of descending age.**

```
SELECT id, age, m.coins_needed, m.power FROM 
(SELECT code, power, MIN(coins_needed) AS coins_needed FROM Wands GROUP BY code, power) AS m
JOIN Wands AS w ON m.code = w.code AND m.power = w.power AND m.coins_needed = w.coins_needed
JOIN Wands_Property AS p ON m.code = p.code
WHERE p.is_evil = 0
ORDER BY m.power DESC, age DESC;
```

**(19) Write a query to output the names of those students whose best friends got offered a higher salary than them. Names must be ordered by the salary amount offered to the best friends. It is guaranteed that no two students got same salary offer.**

```
SELECT s.Name FROM Students AS s 
JOIN Packages AS sp ON s.ID = sp.ID 
JOIN Friends AS f ON s.ID = f.ID
JOIN Packages AS fp ON f.Friend_ID = fp.ID
WHERE sp.Salary < fp.Salary
ORDER BY fp.Salary;
```

**(20) You are given a table, Functions, containing two columns: X and Y. Two pairs (X1, Y1) and (X2, Y2) are said to be symmetric pairs if X1 = Y2 and X2 = Y1. Write a query to output all such symmetric pairs in ascending order by the value of X.**

```
(SELECT f1.X, f1.Y FROM Functions AS f1 
WHERE f1.X = f1.Y GROUP BY f1.X, f1.Y HAVING COUNT(*) > 1)
UNION
(SELECT f1.X, f1.Y FROM Functions AS f1
WHERE EXISTS(SELECT X, Y FROM Functions WHERE f1.X = Y AND f1.Y = X AND f1.X < X))
ORDER BY X;
```

**(21) Write a query to output the start and end dates of projects listed by the number of days it took to complete the project in ascending order. If there is more than one project that have the same number of completion days, then order by the start date of the project.**

```
SELECT Start_Date, MIN(End_Date) FROM
(SELECT Start_Date FROM Projects WHERE Start_Date NOT IN (SELECT End_Date FROM Projects)) AS s,
(SELECT End_Date FROM Projects WHERE End_Date NOT IN (SELECT Start_Date FROM Projects)) AS e
WHERE Start_Date < End_Date
GROUP BY Start_Date
ORDER BY DATEDIFF(MIN(End_Date), Start_Date), Start_Date;
```

**(22) The total score of a hacker is the sum of their maximum scores for all of the challenges. Write a query to print the hacker_id, name, and total score of the hackers ordered by the descending score. If more than one hacker achieved the same total score, then sort the result by ascending hacker_id. Exclude all hackers with a total score of 0 from your result.**

```
SELECT hacker_id, name, SUM(MaxScore) AS TotalScore FROM
    (SELECT Hackers.hacker_id, name, challenge_id, MAX(score) AS MaxScore
    FROM Hackers
    JOIN Submissions ON Hackers.hacker_id = Submissions.hacker_id
    GROUP BY Hackers.hacker_id, name, challenge_id) AS T1
    GROUP BY hacker_id, name
    HAVING TotalScore > 0
    ORDER BY TotalScore DESC, hacker_id
```

**(23) Julia asked her students to create some coding challenges. Write a query to print the hacker_id, name, and the total number of challenges created by each student. Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by hacker_id. If more than one student created the same number of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.**

```
SELECT c.hacker_id, h.name, COUNT(c.challenge_id) AS cnt 
FROM Hackers AS h JOIN Challenges AS c ON h.hacker_id = c.hacker_id
GROUP BY c.hacker_id, h.name HAVING
cnt = (SELECT COUNT(c1.challenge_id) FROM Challenges AS c1 GROUP BY c1.hacker_id ORDER BY COUNT(*) DESC LIMIT 1) OR
cnt NOT IN (SELECT COUNT(c2.challenge_id) FROM Challenges AS c2 GROUP BY c2.hacker_id HAVING c2.hacker_id <> c.hacker_id)
ORDER BY cnt DESC, c.hacker_id;
```

**(24) Query the two cities in STATION with the shortest and longest CITY names, as well as their respective lengths (i.e.: number of characters in the name). If there is more than one smallest or largest city, choose the one that comes first when ordered alphabetically.**

```
SELECT CITY, LENGTH(CITY) FROM STATION ORDER BY LENGTH(CITY), CITY LIMIT 1;
SELECT CITY, LENGTH(CITY) FROM STATION ORDER BY LENGTH(CITY) DESC, CITY LIMIT 1;
```

**(25) Query the list of CITY names starting with vowels (i.e., a, e, i, o, or u) from STATION. Your result cannot contain duplicates.**

```
SELECT DISTINCT(CITY) FROM STATION WHERE CITY REGEXP '^[aeiou]'  
```

**(26) Query the list of CITY names from STATION that do not end with vowels. Your result cannot contain duplicates.**

```
SELECT DISTINCT CITY FROM STATION WHERE CITY REGEXP '[^aeiou]$';
```

**(27) Query the list of CITY names from STATION that either do not start with vowels or do not end with vowels. Your result cannot contain duplicates.**

```
SELECT DISTINCT CITY FROM STATION WHERE CITY REGEXP '^[^aeiou]|[^aeiou]$';
```

**(28) Write a query to print the pattern P(20).**

```
SET @number = 21;
SELECT REPEAT('* ', @number := @number-1) FROM information_schema.tables WHERE @number > 0;
```

**(29) Query the Name of any student in STUDENTS who scored higher than 75 Marks. Order your output by the last three characters of each name. If two or more students both have names ending in the same last three characters (i.e.: Bobby, Robby, etc.), secondary sort them by ascending ID.**

```
SELECT Name FROM STUDENTS WHERE Marks > 75 ORDER BY SUBSTR(Name,-3), ID ASC
```

**(30) Query the list of CITY names from STATION that do not start with vowels and do not end with vowels. Your result cannot contain duplicates.**

```
SELECT DISTINCT city FROM station WHERE city RLIKE '^[^aeiouAEIOU].*[^aeiouAEIOU]$'
```

**(31) Samantha was tasked with calculating the average monthly salaries for all employees in the EMPLOYEES table, but did not realize her keyboard's 0 key was broken until after completing the calculation. She wants your help finding the difference between her miscalculation (using salaries with any zeroes removed), and the actual average salary. Write a query calculating the amount of error and round it up to the next integer.**

```
SELECT CEIL(AVG(Salary) - AVG(REPLACE(Salary, '0', ''))) FROM EMPLOYEES;
```
