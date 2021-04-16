# STV\_STARTUP\_RECOVERY\_STATE<a name="r_STV_STARTUP_RECOVERY_STATE"></a>

Records the state of tables that are temporarily locked during cluster restart operations\. Amazon Redshift places a temporary lock on tables while they are being processed to resolve stale transactions following a cluster restart\. 

STV\_STARTUP\_RECOVERY\_STATE is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_STARTUP_RECOVERY_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_STARTUP_RECOVERY_STATE.html)

## Sample queries<a name="r_STV_STARTUP_RECOVERY_STATE-sample-queries"></a>

To monitor which tables are temporarily locked, execute the following query after a cluster restart\. 

```
select * from STV_STARTUP_RECOVERY_STATE;

  db_id | tbl_id | table_name 
--------+--------+------------
 100044 | 100058 | lineorder  
 100044 | 100068 | part  
 100044 | 100072 | customer   
 100044 | 100192 | supplier  
(4 rows)
```