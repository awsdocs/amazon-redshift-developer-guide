# PG\_TABLE\_DEF<a name="r_PG_TABLE_DEF"></a>

Stores information about table columns\.

PG\_TABLE\_DEF only returns information about tables that are visible to the user\. If PG\_TABLE\_DEF does not return the expected results, verify that the [search\_path](r_search_path.md) parameter is set correctly to include the relevant schemas\.

You can use [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md) to view more comprehensive information about a table, including data distribution skew, key distribution skew, table size, and statistics\. 

## Table columns<a name="r_PG_TABLE_DEF-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_TABLE_DEF.html)

## Example<a name="r_PG_TABLE_DEF-example2"></a>

The following example shows the compound sort key columns for the LINEORDER\_COMPOUND table\.

```
select "column", type, encoding, distkey, sortkey, "notnull" 
from pg_table_def
where tablename = 'lineorder_compound' 
and sortkey <> 0;

column       | type    | encoding | distkey | sortkey | notnull
-------------+---------+----------+---------+---------+--------
lo_orderkey  | integer | delta32k | false   |       1 | true   
lo_custkey   | integer | none     | false   |       2 | true   
lo_partkey   | integer | none     | true    |       3 | true   
lo_suppkey   | integer | delta32k | false   |       4 | true   
lo_orderdate | integer | delta    | false   |       5 | true   
(5 rows)
```

 The following example shows the interleaved sort key columns for the LINEORDER\_INTERLEAVED table\.

```
select "column", type, encoding, distkey, sortkey, "notnull" 
from pg_table_def
where tablename = 'lineorder_interleaved' 
and sortkey <> 0;

column       | type    | encoding | distkey | sortkey | notnull
-------------+---------+----------+---------+---------+--------
lo_orderkey  | integer | delta32k | false   |      -1 | true   
lo_custkey   | integer | none     | false   |       2 | true   
lo_partkey   | integer | none     | true    |      -3 | true   
lo_suppkey   | integer | delta32k | false   |       4 | true   
lo_orderdate | integer | delta    | false   |      -5 | true   
(5 rows)
```

PG\_TABLE\_DEF will only return information for tables in schemas that are included in the search path\. For more information, see [search\_path](r_search_path.md)\.

For example, suppose you create a new schema and a new table, then query PG\_TABLE\_DEF\.

```
create schema demo;
create table demo.demotable (one int);
select * from pg_table_def where tablename = 'demotable';

schemaname|tablename|column| type | encoding | distkey | sortkey | notnull 
----------+---------+------+------+----------+---------+---------+--------
```

The query returns no rows for the new table\. Examine the setting for `search_path`\.

```
show search_path;

  search_path
---------------
 $user, public
(1 row)
```

Add the `demo` schema to the search path and execute the query again\.

```
set search_path to '$user', 'public', 'demo';

select * from pg_table_def where tablename = 'demotable';

schemaname| tablename |column|  type   | encoding |distkey|sortkey| notnull
----------+-----------+------+---------+----------+-------+-------+--------
demo      | demotable | one  | integer | none     | f     |     0 | f
(1 row)
```