## Hacker Rank Challenges that I have solved:

**(1) Query the list of CITY names from STATION that do not start with vowels. Your result cannot contain duplicates.**

- `SELECT DISTINCT CITY FROM STATION WHERE upper(SUBSTR(CITY,1,1)) NOT IN ('A','E','I','O','U');`

**(2) Query the list of CITY names from STATION which have vowels (i.e., a, e, i, o, and u) as both their first and last characters. Your result cannot contain duplicates.**

- `SELECT CITY FROM STATION WHERE UPPER(SUBSTR(CITY,1,1)) IN ('A','E','I','O','U') AND UPPER(SUBSTR(CITY,-1,1)) IN ('A','E','I','O','U');`

**(3) find the difference between the total number of CITY entries in the table and the number of distinct CITY entries in the table.**

- `SELECT COUNT(*) - COUNT(DISTINCT(CITY)) FROM STATION;`

**(4) Write a query identifying the type of each record in the TRIANGLES table using its three side lengths.**

- `SELECT IF(A+B>C AND A+C>B AND B+C>A, IF(A=B AND B=C, 'Equilateral', IF(A=B OR B=C OR A=C, 'Isosceles', 'Scalene')), 'Not A Triangle') FROM TRIANGLES;`

**(5) Query the greatest value of the Northern Latitudes (LAT_N) from STATION that is less than 137.2345. Truncate your answer to 4 decimal places.**

- `SELECT ROUND(MAX(LAT_N), 4) FROM STATION WHERE LAT_N < 137.2345;`

**(6) Query the Western Longitude (LONG_W) for the largest Northern Latitude (LAT_N) in STATION that is less than 137.2345. Round your answer to 4 decimal places.**

- `SELECT ROUND(LONG_W, 4) FROM STATION WHERE LAT_N < 137.2345 ORDER BY LAT_N DESC LIMIT 1;`

**(7) Given the CITY and COUNTRY tables, query the names of all cities where the CONTINENT is 'Africa'.**

- `SELECT CI.NAME FROM CITY AS CI JOIN COUNTRY AS CO ON CO.CODE = CI.COUNTRYCODE WHERE CO.CONTINENT = 'Africa';`

**(8) Given the CITY and COUNTRY tables, query the names of all the continents (COUNTRY.Continent) and their respective average city populations (CITY.Population) rounded down to the nearest integer.**

- `SELECT CO.CONTINENT, FLOOR(AVG(CI.POPULATION)) FROM CITY AS CI JOIN COUNTRY AS CO ON CO.CODE = CI.COUNTRYCODE GROUP BY CO.CONTINENT;`

**(9) You are given a table, BST, containing two columns: N and P, where N represents the value of a node in Binary Tree, and P is the parent of N. Write a query to find the node type of Binary Tree ordered by the value of the node (root, leaf, inner).**

- `SELECT N, IF(P IS NULL, 'Root', IF(B.N IN (SELECT P FROM BST), 'Inner', 'Leaf')) FROM BST AS B ORDER BY N;`

**(10) Query an alphabetically ordered list of all names in OCCUPATIONS, immediately followed by the first letter of each profession as a parenthetical (i.e.: enclosed in parentheses). For example: AnActorName(A), ADoctorName(D), AProfessorName(P), and ASingerName(S). Query the number of ocurrences of each occupation in OCCUPATIONS. Sort the occurrences in ascending order.**

```
SELECT CONCAT(Name, '(' ,SUBSTR(Occupation,1,1), ')') FROM OCCUPATIONS ORDER BY Name;
SELECT CONCAT('There are a total of ', COUNT(*), ' ', LOWER(Occupation), 's.') FROM OCCUPATIONS GROUP BY Occupation ORDER BY COUNT(Occupation), Occupation; 
```
