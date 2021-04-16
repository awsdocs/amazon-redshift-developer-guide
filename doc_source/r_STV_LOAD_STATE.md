# STV\_LOAD\_STATE<a name="r_STV_LOAD_STATE"></a>

Use the STV\_LOAD\_STATE table to find information about current state of ongoing COPY statements\.

The COPY command updates this table after every million records are loaded\.

STV\_LOAD\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_LOAD_STATE-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_LOAD_STATE.html)

## Sample query<a name="r_STV_LOAD_STATE-sample-query2"></a>

To view the progress of each slice for a COPY command, type the following query\. This example uses the PG\_LAST\_COPY\_ID\(\) function to retrieve information for the last COPY command\.

```
select slice , bytes_loaded, bytes_to_load , pct_complete from stv_load_state where query = pg_last_copy_id();

 slice | bytes_loaded | bytes_to_load | pct_complete 
-------+--------------+---------------+--------------
     2 |            0 |             0 |            0
     3 |     12840898 |      39104640 |           32
(2 rows)
```