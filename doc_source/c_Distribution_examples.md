# Distribution examples<a name="c_Distribution_examples"></a>

The following examples show how data is distributed according to the options that you define in the CREATE TABLE statement\.

## DISTKEY examples<a name="c_Distribution_examples-distkey-examples"></a>

Look at the schema of the USERS table in the TICKIT database\. USERID is defined as the SORTKEY column and the DISTKEY column: 

```
select "column", type, encoding, distkey, sortkey 
from pg_table_def where tablename = 'users';
    
    column     |          type          | encoding | distkey | sortkey
---------------+------------------------+----------+---------+---------
 userid        | integer                | none     | t       |       1
 username      | character(8)           | none     | f       |       0
 firstname     | character varying(30)  | text32k  | f       |       0

...
```

USERID is a good choice for the distribution column on this table\. If you query the SVV\_DISKUSAGE system view, you can see that the table is very evenly distributed\. Column numbers are zero\-based, so USERID is column 0\.

```
select slice, col, num_values as rows, minvalue, maxvalue
from svv_diskusage
where name='users' and col=0 and rows>0
order by slice, col;

slice| col | rows  | minvalue | maxvalue
-----+-----+-------+----------+----------
0    | 0   | 12496 | 4        | 49987
1    | 0   | 12498 | 1        | 49988
2    | 0   | 12497 | 2        | 49989
3    | 0   | 12499 | 3        | 49990
(4 rows)
```

The table contains 49,990 rows\. The rows \(num\_values\) column shows that each slice contains about the same number of rows\. The minvalue and maxvalue columns show the range of values on each slice\. Each slice includes nearly the entire range of values, so there's a good chance that every slice will participate in executing a query that filters for a range of user IDs\.

This example demonstrates distribution on a small test system\. The total number of slices is typically much higher\.

If you commonly join or group using the STATE column, you might choose to distribute on the STATE column\. The following examples shows that if you create a new table with the same data as the USERS table, but you set the DISTKEY to the STATE column, the distribution will not be as even\. Slice 0 \(13,587 rows\) holds approximately 30% more rows than slice 3 \(10,150 rows\)\. In a much larger table, this amount of distribution skew could have an adverse impact on query processing\.

```
create table userskey distkey(state) as select * from users;

select slice, col, num_values as rows, minvalue, maxvalue from svv_diskusage
where name = 'userskey' and col=0 and rows>0
order by slice, col;

slice | col | rows  | minvalue | maxvalue
------+-----+-------+----------+----------
    0 |   0 | 13587 |        5 |    49989
    1 |   0 | 11245 |        2 |    49990
    2 |   0 | 15008 |        1 |    49976
    3 |   0 | 10150 |        4 |    49986
(4 rows)
```

## DISTSTYLE EVEN example<a name="c_Distribution_examples-diststyle-even-example"></a>

If you create a new table with the same data as the USERS table but set the DISTSTYLE to EVEN, rows are always evenly distributed across slices\. 

```
create table userseven diststyle even as 
select * from users;

select slice, col, num_values as rows, minvalue, maxvalue from svv_diskusage
where name = 'userseven' and col=0 and rows>0
order by slice, col;

slice | col | rows  | minvalue | maxvalue
------+-----+-------+----------+----------
    0 |   0 | 12497 |        4 |    49990
    1 |   0 | 12498 |        8 |    49984
    2 |   0 | 12498 |        2 |    49988
    3 |   0 | 12497 |        1 |    49989  
(4 rows)
```

However, because distribution is not based on a specific column, query processing can be degraded, especially if the table is joined to other tables\. The lack of distribution on a joining column often influences the type of join operation that can be performed efficiently\. Joins, aggregations, and grouping operations are optimized when both tables are distributed and sorted on their respective joining columns\.

## DISTSTYLE ALL example<a name="c_Distribution_examples-diststyle-all-example"></a>

If you create a new table with the same data as the USERS table but set the DISTSTYLE to ALL, all the rows are distributed to the first slice of each node\. 

```
select slice, col, num_values as rows, minvalue, maxvalue from svv_diskusage
where name = 'usersall' and col=0 and rows > 0
order by slice, col;

slice | col | rows  | minvalue | maxvalue
------+-----+-------+----------+----------
    0 |   0 | 49990 |        4 |    49990
    2 |   0 | 49990 |        2 |    49990

(4 rows)
```