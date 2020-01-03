
**(1) A table containing the students enrolled in a yearly course has incorrect data in records with ids between 20 and 100 (inclusive). Write a query that updates the field 'year' of every faulty record to 2015.**

```
UPDATE enrollments SET year = 2015 WHERE id BETWEEN 19 AND 101;
```

**(2) Information about pets is kept in two separate tables. Write a query that select all distinct pet names.**

```
SELECT DISTINCT name FROM (SELECT name FROM dogs
                           UNION
                           SELECT name FROM cats);
```

**(3) Write a query that selects userId and average session duration for each user who has more than one session.**

```
SELECT userId, AVG(duration) FROM sessions GROUP BY userId HAVING count(id) > 1;
```
