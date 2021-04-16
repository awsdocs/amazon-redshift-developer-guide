# SVV\_TABLE\_INFO<a name="r_SVV_TABLE_INFO"></a>

Shows summary information for tables in the database\. The view filters system tables and shows only user\-defined tables\. 

You can use the SVV\_TABLE\_INFO view to diagnose and address table design issues that can influence query performance, including issues with compression encoding, distribution keys, sort style, data distribution skew, table size, and statistics\. The SVV\_TABLE\_INFO view doesn't return any information for empty tables\.

The SVV\_TABLE\_INFO view summarizes information from the [STV\_BLOCKLIST](r_STV_BLOCKLIST.md), [STV\_PARTITIONS](r_STV_PARTITIONS.md), [STV\_TBL\_PERM](r_STV_TBL_PERM.md), and [STV\_SLICES](r_STV_SLICES.md) system tables and from the [PG\_DATABASE](https://www.postgresql.org/docs/8.0/static/catalog-pg-database.html), [PG\_ATTRIBUTE](https://www.postgresql.org/docs/8.0/static/catalog-pg-attribute.html), [PG\_CLASS](https://www.postgresql.org/docs/8.0/static/catalog-pg-class.html), [PG\_NAMESPACE](https://www.postgresql.org/docs/8.0/static/catalog-pg-namespace.html), and [PG\_TYPE](https://www.postgresql.org/docs/8.0/static/catalog-pg-type.html) catalog tables\. 

SVV\_TABLE\_INFO is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\. To permit a user to query the view, grant SELECT privilege on SVV\_TABLE\_INFO to the user\.

## Table columns<a name="SVV_TABLE_INFO-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_TABLE_INFO.html)

## Sample queries<a name="SVV_TABLE_INFO-sample-queries"></a>

The following example shows encoding, distribution style, sorting, and data skew for all user\-defined tables in the database\. Here, "table" must be enclosed in double quotation marks because it is a reserved word\.

```
select "table", encoded, diststyle, sortkey1, skew_sortkey1, skew_rows
from svv_table_info
order by 1;

table          | encoded | diststyle       | sortkey1     | skew_sortkey1 | skew_rows
---------------+---------+-----------------+--------------+---------------+----------
category       | N       | EVEN            |              |               |          
date           | N       | ALL             | dateid       |          1.00 |          
event          | Y       | KEY(eventid)    | dateid       |          1.00 |      1.02
listing        | Y       | KEY(listid)     | dateid       |          1.00 |      1.01
sales          | Y       | KEY(listid)     | dateid       |          1.00 |      1.02
users          | Y       | KEY(userid)     | userid       |          1.00 |      1.01
venue          | N       | ALL             | venueid      |          1.00 |          
(7 rows)
```