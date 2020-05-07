# Examples of catalog queries<a name="c_join_PG_examples"></a>

The following queries show a few of the ways in which you can query the catalog tables to get useful information about an Amazon Redshift database\.

## View table ID, database, schema, and table name<a name="c_join_PG_examples-view-tableid-db-schema-tablename"></a>

The following view definition joins the STV\_TBL\_PERM system table with the PG\_CLASS, PG\_NAMESPACE, and PG\_DATABASE system catalog tables to return the table ID, database name, schema name, and table name\.

```
create view tables_vw as
select distinct(id) table_id
,trim(datname)   db_name
,trim(nspname)   schema_name
,trim(relname)   table_name
from stv_tbl_perm
join pg_class on pg_class.oid = stv_tbl_perm.id
join pg_namespace on pg_namespace.oid = relnamespace
join pg_database on pg_database.oid = stv_tbl_perm.db_id;
```

The following example returns the information for table ID 117855\.

```
select * from tables_vw where table_id = 117855;
```

```
table_id | db_name   | schema_name | table_name
---------+-----------+-------------+-----------
  117855 |       dev | public      | customer
```

## List the number of columns per Amazon Redshift table<a name="c_join_PG_examples-list-the-number-of-columns-per-amazon-redshift-table"></a>

The following query joins some catalog tables to find out how many columns each Amazon Redshift table contains\. Amazon Redshift table names are stored in both PG\_TABLES and STV\_TBL\_PERM; where possible, use PG\_TABLES to return Amazon Redshift table names\.

This query does not involve any Amazon Redshift tables\.

```
select nspname, relname, max(attnum) as num_cols
from pg_attribute a, pg_namespace n, pg_class c
where n.oid = c.relnamespace and  a.attrelid = c.oid
and c.relname not like '%pkey'
and n.nspname not like 'pg%'
and n.nspname not like 'information%'
group by 1, 2
order by 1, 2;

nspname | relname  | num_cols
--------+----------+----------
public  | category |        4
public  | date     |        8
public  | event    |        6
public  | listing  |        8
public  | sales    |       10
public  | users    |       18
public  | venue    |        5
(7 rows)
```

## List the schemas and tables in a database<a name="c_join_PG_examples-list-the-schemas-and-tables-in-a-database"></a>

The following query joins STV\_TBL\_PERM to some PG tables to return a list of tables in the TICKIT database and their schema names \(NSPNAME column\)\. The query also returns the total number of rows in each table\. \(This query is helpful when multiple schemas in your system have the same table names\.\)

```
select datname, nspname, relname, sum(rows) as rows
from pg_class, pg_namespace, pg_database, stv_tbl_perm
where pg_namespace.oid = relnamespace
and pg_class.oid = stv_tbl_perm.id
and pg_database.oid = stv_tbl_perm.db_id
and datname ='tickit'
group by datname, nspname, relname
order by datname, nspname, relname;

datname | nspname | relname  |  rows
--------+---------+----------+--------
tickit  | public  | category |     11
tickit  | public  | date     |    365
tickit  | public  | event    |   8798
tickit  | public  | listing  | 192497
tickit  | public  | sales    | 172456
tickit  | public  | users    |  49990
tickit  | public  | venue    |    202
(7 rows)
```

## List table IDs, data types, column names, and table names<a name="c_join_PG_examples-list-table-ids-data-types-column-names-and-table-names"></a>

The following query lists some information about each user table and its columns: the table ID, the table name, its column names, and the data type of each column:

```
select distinct attrelid, rtrim(name), attname, typname
from pg_attribute a, pg_type t, stv_tbl_perm p
where t.oid=a.atttypid and a.attrelid=p.id
and a.attrelid between 100100 and 110000
and typname not in('oid','xid','tid','cid')
order by a.attrelid asc, typname, attname;

attrelid |  rtrim   |    attname     |  typname
---------+----------+----------------+-----------
  100133 | users    | likebroadway   | bool
  100133 | users    | likeclassical  | bool
  100133 | users    | likeconcerts   | bool
...
  100137 | venue    | venuestate     | bpchar
  100137 | venue    | venueid        | int2
  100137 | venue    | venueseats     | int4
  100137 | venue    | venuecity      | varchar
...
```

## Count the number of data blocks for each column in a table<a name="c_join_PG_examples-count-the-number-of-data-blocks-for-each-column-in-a-table"></a>

The following query joins the STV\_BLOCKLIST table to PG\_CLASS to return storage information for the columns in the SALES table\.

```
select col, count(*)
from stv_blocklist s, pg_class p
where s.tbl=p.oid and relname='sales'
group by col
order by col;

col | count
----+-------
  0 |     4
  1 |     4
  2 |     4
  3 |     4
  4 |     4
  5 |     4
  6 |     4
  7 |     4
  8 |     4
  9 |     8
 10 |     4
 12 |     4
 13 |     8
(13 rows)
```