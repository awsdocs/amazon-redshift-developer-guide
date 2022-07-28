# STL\_UNDONE<a name="r_STL_UNDONE"></a>

Displays information about transactions that have been undone\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_UNDONE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_UNDONE.html)

## Sample query<a name="r_STL_UNDONE-sample-query"></a>

To view a concise log of all undone transactions, type the following command: 

```
select xact_id, xact_id_undone, table_id from stl_undone;
```

This command returns the following sample output: 

```
 xact_id | xact_id_undone | table_id
---------+----------------+----------
1344 |           1344 |   100192
1326 |           1326 |   100192
1551 |           1551 |   100192
(3 rows)
```