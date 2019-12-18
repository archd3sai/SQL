**(1) You are given one table:**
```
CREATE TABLE orders
(
    order_number int PRIMARY KEY,
    client varchar(20),
    revenue int,
    fixed_transport_cost int,
    income int,
    order_date date
)
```
**Select clients whose first order was placed in 2017 and calculate how much income was genereted in 2018 (total) per each of them.**

```
SELECT Client,
 (SELECT SUM(income) FROM orders AS O1
     WHERE EXTRACT(YEAR FROM order_date) = 2018 AND O1.Client = T.Client) 
 
 FROM
(SELECT Client, MIN(order_date) AS First_Order FROM orders GROUP BY Client) AS T
WHERE EXTRACT(YEAR FROM First_Order) = 2017

```
