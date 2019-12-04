## Hacker Rank Challenges that I have solved:

**(1) Query the list of CITY names from STATION that do not start with vowels. Your result cannot contain duplicates.**

SELECT DISTINCT CITY FROM STATION WHERE upper(SUBSTR(CITY,1,1)) NOT IN ('A','E','I','O','U');

**(2) Query the list of CITY names from STATION which have vowels (i.e., a, e, i, o, and u) as both their first and last characters. Your result cannot contain duplicates.**

SELECT CITY FROM STATION WHERE UPPER(SUBSTR(CITY,1,1)) IN ('A','E','I','O','U') AND UPPER(SUBSTR(CITY,-1,1)) IN ('A','E','I','O','U')

**(3) find the difference between the total number of CITY entries in the table and the number of distinct CITY entries in the table.**

SELECT COUNT(*) - COUNT(DISTINCT(CITY)) FROM STATION
