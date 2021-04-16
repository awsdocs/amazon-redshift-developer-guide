# BETWEEN range condition<a name="r_range_condition"></a>

A `BETWEEN` condition tests expressions for inclusion in a range of values, using the keywords `BETWEEN` and `AND`\. 

## Syntax<a name="r_range_condition-synopsis"></a>

```
expression [ NOT ] BETWEEN expression AND expression
```

Expressions can be numeric, character, or datetime data types, but they must be compatible\. The range is inclusive\.

## Examples<a name="r_range_condition-examples"></a>

The first example counts how many transactions registered sales of either 2, 3, or 4 tickets: 

```
select count(*) from sales
where qtysold between 2 and 4;

count
--------
104021
(1 row)
```

The range condition includes the begin and end values\.

```
select min(dateid), max(dateid) from sales
where dateid between 1900 and 1910;

min  | max 
-----+-----
1900 | 1910
```

The first expression in a range condition must be the lesser value and the second expression the greater value\. The following example will always return zero rows due to the values of the expressions: 

```
select count(*) from sales
where qtysold between 4 and 2;

count
-------
0
(1 row)
```

However, applying the NOT modifier will invert the logic and produce a count of all rows: 

```
select count(*) from sales
where qtysold not between 4 and 2;

count
--------
172456
(1 row)
```

The following query returns a list of venues with 20000 to 50000 seats: 

```
select venueid, venuename, venueseats from venue
where venueseats between 20000 and 50000
order by venueseats desc;

venueid |       venuename               | venueseats
---------+-------------------------------+------------
116 | Busch Stadium                 |      49660
106 | Rangers BallPark in Arlington |      49115
96 | Oriole Park at Camden Yards   |      48876
...
(22 rows)
```