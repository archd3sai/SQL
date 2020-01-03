
(1) A table containing the students enrolled in a yearly course has incorrect data in records with ids between 20 and 100 (inclusive). Write a query that updates the field 'year' of every faulty record to 2015.

```
UPDATE enrollments SET year = 2015 WHERE id BETWEEN 19 AND 101
```
