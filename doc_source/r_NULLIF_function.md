# NULLIF expression<a name="r_NULLIF_function"></a>

## Syntax<a name="r_NULLIF_function-synopsis"></a>

The NULLIF expression compares two arguments and returns null if the arguments are equal\. If they are not equal, the first argument is returned\. This expression is the inverse of the NVL or COALESCE expression\.

```
NULLIF ( expression1, expression2 )
```

## Arguments<a name="r_NULLIF_function-arguments"></a>

 *expression1, expression2*   
The target columns or expressions that are compared\. The return type is the same as the type of the first expression\. The default column name of the NULLIF result is the column name of the first expression\.

## Examples<a name="r_NULLIF_function-examples"></a>

In the following example, the query returns null when the LISTID and SALESID values match:

```
select nullif(listid,salesid), salesid
from sales where salesid<10 order by 1, 2 desc;

listid  | salesid
--------+---------
     4  |       2
     5  |       4
     5  |       3
     6  |       5
     10 |       9
     10 |       8
     10 |       7
     10 |       6
        |       1
(9 rows)
```

You can use NULLIF to ensure that empty strings are always returned as nulls\. In the example below, the NULLIF expression returns either a null value or a string that contains at least one character\.

```
insert into category
values(0,'','Special','Special');

select nullif(catgroup,'') from category
where catdesc='Special';

catgroup
----------
null
(1 row)
```

NULLIF ignores trailing spaces\. If a string is not empty but contains spaces, NULLIF still returns null:

```
create table nulliftest(c1 char(2), c2 char(2));

insert into nulliftest values ('a','a ');

insert into nulliftest values ('b','b');


select nullif(c1,c2) from nulliftest;
c1
------
null
null
(2 rows)
```