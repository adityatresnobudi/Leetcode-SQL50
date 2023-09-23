# 584. Find Customer Referee

## Question

```
Table: Customer
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| referee_id  | int     |
+-------------+---------+
In SQL, id is the primary key column for this table.
Each row of this table indicates the id of a customer, their name, and the id of the customer who referred them.
```

Find the names of the customer that are not referred by the customer with `id = 2`.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
```
Customer Table:
+----+------+------------+
| id | name | referee_id |
+----+------+------------+
| 1  | Will | null       |
| 2  | Jane | null       |
| 3  | Alex | 2          |
| 4  | Bill | null       |
| 5  | Zack | 1          |
| 6  | Mark | 2          |
+----+------+------------+
```
Output: 
```
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```
## Solution
```
# Write your MySQL query statement below
SELECT
    name
FROM
    Customer
WHERE
    referee_id IS NULL OR referee_id != 2
```

The idea is we want to display name only from table Customer which haven't got a referee assigned to them `(referee_id = null)` or already got a referee assigned to them but is not referee number 2 `(referee_id != 2)`.