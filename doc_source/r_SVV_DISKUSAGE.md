# SVV\_DISKUSAGE<a name="r_SVV_DISKUSAGE"></a>

Amazon Redshift creates the SVV\_DISKUSAGE system view by joining the STV\_TBL\_PERM and STV\_BLOCKLIST tables\. The SVV\_DISKUSAGE view contains information about data allocation for the tables in a database\.

Use aggregate queries with SVV\_DISKUSAGE, as the following examples show, to determine the number of disk blocks allocated per database, table, slice, or column\. Each data block uses 1 MB\. You can also use [STV\_PARTITIONS](r_STV_PARTITIONS.md) to view summary information about disk utilization\.

SVV\_DISKUSAGE is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVV_DISKUSAGE-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DISKUSAGE.html)

## Sample queries<a name="r_SVV_DISKUSAGE-sample-queries"></a>

SVV\_DISKUSAGE contains one row per allocated disk block, so a query that selects all the rows potentially returns a very large number of rows\. We recommend using only aggregate queries with SVV\_DISKUSAGE\.

Return the highest number of blocks ever allocated to column 6 in the USERS table \(the EMAIL column\):

```
select db_id, trim(name) as tablename, max(blocknum)
from svv_diskusage
where name='users' and col=6
group by db_id, name;

db_id  | tablename | max
--------+-----------+-----
175857 | users     |   2
(1 row)
```

The following query returns similar results for all of the columns in a large 10\-column table called SALESNEW\. \(The last three rows, for columns 10 through 12, are for the hidden metadata columns\.\) 

```
select db_id, trim(name) as tablename, col, tbl, max(blocknum)
from svv_diskusage
where name='salesnew'
group by db_id, name, col, tbl
order by db_id, name, col, tbl;

db_id  | tablename  | col |  tbl   | max
--------+------------+-----+--------+-----
175857 | salesnew   |   0 | 187605 | 154
175857 | salesnew   |   1 | 187605 | 154
175857 | salesnew   |   2 | 187605 | 154
175857 | salesnew   |   3 | 187605 | 154
175857 | salesnew   |   4 | 187605 | 154
175857 | salesnew   |   5 | 187605 |  79
175857 | salesnew   |   6 | 187605 |  79
175857 | salesnew   |   7 | 187605 | 302
175857 | salesnew   |   8 | 187605 | 302
175857 | salesnew   |   9 | 187605 | 302
175857 | salesnew   |  10 | 187605 |   3
175857 | salesnew   |  11 | 187605 |   2
175857 | salesnew   |  12 | 187605 | 296
(13 rows)
```