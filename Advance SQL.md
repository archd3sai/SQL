source: studybyyourself advanced sql

## Recursive Association
- joining between a table and that table itself, which is known under self-join.

(1) Top Level:

```
SELECT First_name, Last_name
FROM Worker
WHERE Boss_id IS NULL
```

(2) First Level:

```
SELECT W2.First_name, W2.Last_name, W1.Last_name AS DirectBoss
FROM Worker AS W1, Worker AS W2
WHERE W2.Boss_id = W1.ID
      AND W1.First_name = 'Eddy' AND W1.Last_name ='Fisher'
```

(3) Second Level:

```
SELECT W3.First_name, W3.Last_name, W2.Last_name AS DirectBoss
FROM Worker AS W1, Worker AS W2, Worker AS W3
WHERE W3.Boss_id = W2.ID
      AND W2.Boss_id = W1.ID
      AND W1.First_name = 'Eddy' AND W1.Last_name ='Fisher' 
```

## Derived table

```
SELECT P.ID, P.post_title, IFNULL(derivLikes.nbLikes,0) AS Likes, IFNULL(derivComm.nbComments,0) AS Comments
FROM Posts AS P
LEFT JOIN
   ( SELECT post_ID, COUNT(*) AS nbLikes
     FROM Likes
     GROUP BY post_ID ) AS derivLikes ON P.ID = derivLikes.post_ID
LEFT JOIN 
   ( SELECT post_ID, COUNT(*) AS nbComments
     FROM Comments
     GROUP BY post_ID ) AS derivComm ON P.ID = derivComm.post_ID
```

## 
