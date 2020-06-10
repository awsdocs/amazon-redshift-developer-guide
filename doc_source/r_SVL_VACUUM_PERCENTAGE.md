# SVL\_VACUUM\_PERCENTAGE<a name="r_SVL_VACUUM_PERCENTAGE"></a>

The SVL\_VACUUM\_PERCENTAGE view reports the percentage of data blocks allocated to a table after performing a vacuum\. This percentage number shows how much disk space was reclaimed\. See the [VACUUM](r_VACUUM_command.md) command for more information about the vacuum utility\.

SVL\_VACUUM\_PERCENTAGE is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_VACUUM_PERCENTAGE-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_VACUUM_PERCENTAGE.html)

## Sample query<a name="r_SVL_VACUUM_PERCENTAGE-sample-query"></a>

The following query displays the percentage for a specific operation on table 100238: 

```
select * from svl_vacuum_percentage
where table_id=100238 and xid=2200;

xid  | table_id | percentage
-----+----------+------------
1337 |   100238 |         60
(1 row)
```

After this vacuum operation, the table contained 60 percent of the original blocks\.