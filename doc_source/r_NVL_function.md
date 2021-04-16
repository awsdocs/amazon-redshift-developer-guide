# NVL expression<a name="r_NVL_function"></a>

An NVL expression is identical to a COALESCE expression\. NVL and COALESCE are synonyms\.

## Syntax<a name="r_NVL_function-synopsis"></a>

```
NVL | COALESCE ( expression, expression, ... )
```

An NVL or COALESCE expression returns the value of the first expression in the list that is not null\. If all expressions are null, the result is null\. When a non\-null value is found, the remaining expressions in the list are not evaluated\.

This type of expression is useful when you want to return a backup value for something when the preferred value is missing or null\. For example, a query might return one of three phone numbers \(cell, home, or work, in that order\), whichever is found first in the table \(not null\)\.

## Examples<a name="r_NVL_function-examples"></a>

Create a table with START\_DATE and END\_DATE columns, insert some rows that include null values, then apply an NVL expression to the two columns\.

```
create table datetable (start_date date, end_date date);
```

```
insert into datetable values ('2008-06-01','2008-12-31');
insert into datetable values (null,'2008-12-31');
insert into datetable values ('2008-12-31',null);
```

```
select nvl(start_date, end_date)
from datetable
order by 1;

coalesce
------------
2008-06-01
2008-12-31
2008-12-31
```

 The default column name for an NVL expression is COALESCE\. The following query would return the same results:

```
select coalesce(start_date, end_date)
from datetable
order by 1;
```

If you expect a query to return null values for certain functions or columns, you can use an NVL expression to replace the nulls with some other value\. For example, aggregate functions, such as SUM, return null values instead of zeroes when they have no rows to evaluate\. You can use an NVL expression to replace these null values with `0.0`:

```
select nvl(sum(sales), 0.0) as sumresult, ...
```