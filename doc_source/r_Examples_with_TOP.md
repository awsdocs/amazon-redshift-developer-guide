# Examples with TOP<a name="r_Examples_with_TOP"></a>

Return any 10 rows from the SALES table\. Because no ORDER BY clause is specified, the set of rows that this query returns is unpredictable\.

```
select top 10 *
from sales;
```

The following query is functionally equivalent, but uses a LIMIT clause instead of a TOP clause:

```
select *
from sales
limit 10;
```

Return the first 10 rows from the SALES table, ordered by the QTYSOLD column in descending order\.

```
select top 10 qtysold, sellerid 
from sales
order by qtysold desc, sellerid;

qtysold | sellerid
--------+----------
8 |      518
8 |      520
8 |      574
8 |      718
8 |      868
8 |     2663
8 |     3396
8 |     3726
8 |     5250
8 |     6216
(10 rows)
```

Return the first two QTYSOLD and SELLERID values from the SALES table, ordered by the QTYSOLD column:

```
select top 2 qtysold, sellerid
from sales
order by qtysold desc, sellerid;

qtysold | sellerid
--------+----------
8 |      518
8 |      520
(2 rows)
```