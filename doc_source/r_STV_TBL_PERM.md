# STV\_TBL\_PERM<a name="r_STV_TBL_PERM"></a>

The STV\_TBL\_PERM table contains information about the permanent tables in Amazon Redshift, including temporary tables created by a user for the current session\. STV\_TBL\_PERM contains information for all tables in all databases\.

This table differs from [STV\_TBL\_TRANS](r_STV_TBL_TRANS.md), which contains information about transient database tables that the system creates during query processing\.

STV\_TBL\_PERM is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_TBL_PERM-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_TBL_PERM.html)

## Sample queries<a name="r_STV_TBL_PERM-sample-queries"></a>

The following query returns a list of distinct table IDs and names: 

```
select distinct id, name
from stv_tbl_perm order by name;

   id   |          name
--------+-------------------------
 100571 | category
 100575 | date
 100580 | event
 100596 | listing
 100003 | padb_config_harvest
 100612 | sales
...
```

Other system tables use table IDs, so knowing which table ID corresponds to a certain table can be very useful\. In this example, SELECT DISTINCT is used to remove the duplicates \(tables are distributed across multiple slices\)\.

To determine the number of blocks used by each column in the VENUE table, type the following query: 

```
select col, count(*)
from stv_blocklist, stv_tbl_perm
where stv_blocklist.tbl = stv_tbl_perm.id
and stv_blocklist.slice = stv_tbl_perm.slice
and stv_tbl_perm.name = 'venue'
group by col
order by col;

 col | count
-----+-------
   0 |     8
   1 |     8
   2 |     8
   3 |     8
   4 |     8
   5 |     8
   6 |     8
   7 |     8
(8 rows)
```

## Usage notes<a name="r_STV_TBL_PERM-usage-notes"></a>

The ROWS column includes counts of deleted rows that have not been vacuumed \(or have been vacuumed but with the SORT ONLY option\)\. Therefore, the SUM of the ROWS column in the STV\_TBL\_PERM table might not match the COUNT\(\*\) result when you query a given table directly\. For example, if 2 rows are deleted from VENUE, the COUNT\(\*\) result is 200 but the SUM\(ROWS\) result is still 202: 

```
delete from venue
where venueid in (1,2);

select count(*) from venue;
count
-------
200
(1 row)

select trim(name) tablename, sum(rows)
from stv_tbl_perm where name='venue' group by name;

tablename | sum
-----------+-----
venue     | 202
(1 row)
```

To synchronize the data in STV\_TBL\_PERM, run a full vacuum the VENUE table\.

```
vacuum venue;

select trim(name) tablename, sum(rows)
from stv_tbl_perm
where name='venue'
group by name;

tablename | sum
-----------+-----
venue     | 200
(1 row)
```