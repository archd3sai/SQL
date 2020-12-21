# Hackerrank IBM test

```
SELECT F.ACTIVITY FROM FRIENDS F
GROUP BY F.ACTIVITY
HAVING COUNT(*) 
NOT IN(
(SELECT MAX(TOTAL) FROM (SELECT COUNT(*) AS TOTAL
FROM FRIENDS GROUP BY ACTIVITY) AS T1),

(SELECT MIN(TOTAL) FROM (SELECT COUNT(*) AS TOTAL
FROM FRIENDS GROUP BY ACTIVITY) AS T2)
)
```

### Similar Question
```
My data:

    product Table:
    Category_ID Product_ID Price
    1           12         120
    1           19         234
    2           10         129
    3           34         145
    3           11         100
    4           8          56
```
I would like to find categories whose total price is neither maximum nor minimum using MySQL.
```
Results:

    Category_ID Total_Price
    2           129
    3           245
```

Answer:
```
select *
from (
    select category_id, sum(price) as sum_price,
        rank() over(order by sum(price)) rn_asc,
        rank() over(order by sum(price) desc) rn_desc
    from product p
    group by category_id
) p
where rn_asc > 1 and rn_desc > 1
```
