# SELECT DISTINCT examples<a name="r_DISTINCT_examples"></a>

Return a list of different category groups from the CATEGORY table:

```
select distinct catgroup from category
order by 1;

catgroup
----------
Concerts
Shows
Sports
(3 rows)
```

Return the distinct set of week numbers for December 2008:

```
select distinct week, month, year 
from date
where month='DEC' and year=2008
order by 1, 2, 3;

week | month | year
-----+-------+------
49 | DEC   | 2008
50 | DEC   | 2008
51 | DEC   | 2008
52 | DEC   | 2008
53 | DEC   | 2008
(5 rows)
```