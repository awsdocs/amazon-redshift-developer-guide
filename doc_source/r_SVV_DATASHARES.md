# SVV\_DATASHARES<a name="r_SVV_DATASHARES"></a>

Use SVV\_DATASHARES to view a list of datashares created on the cluster, and datashares shared with the cluster\. 

SVV\_DATASHARES is visible to superusers, owners of datashares, or users who have the ALTER or USAGE privilege on the datashare\. Other users can't see any rows\. For information on the ALTER or USAGE privilege, see [GRANT](r_GRANT.md)\.

## Table columns<a name="r_SVV_DATASHARES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARES.html)

## Sample query<a name="r_SVV_DATASHARES-sample-query"></a>

The following example returns the output for SVV\_DATASHARES\.

```
SELECT share_owner, source_database, share_type, is_publicaccessible
FROM svv_datashares
WHERE share_name LIKE 'tickit_datashare%'
AND source_database = 'dev';
    
  share_owner | source_database | share_type  | is_publicaccessible  
--------------+-----------------+-------------+----------------------
     100      |      dev        |   OUTBOUND  |        True
(1 rows)
```

The following example returns the output for SVV\_DATASHARES for outbound datashares\.

```
SELECT share_name, share_owner, btrim(source_database), btrim(consumer_database), share_type, is_publicaccessible, share_acl, btrim(producer_account), btrim(producer_namespace), btrim(managed_by) FROM svv_datashares WHERE share_type = 'OUTBOUND';
                
   share_name   | share_owner | source_database | consumer_database | share_type | is_publicaccessible | share_acl | producer_account|         producer_namespace           | managed_by 
----------------+-------------+-----------------+-------------------+------------+---------------------+-----------+-----------------+--------------------------------------+------------
    salesshare  |      1      |       dev       |                   |  OUTBOUND  |        True         |           |   123456789012  | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d |    
 marketingshare |      1      |       dev       |                   |  OUTBOUND  |        True         |           |   123456789012  | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d |
```

The following example returns the output for SVV\_DATASHARES for inbound datashares\.

```
SELECT share_name, share_owner, btrim(source_database), btrim(consumer_database), share_type, is_publicaccessible, share_acl, btrim(producer_account), btrim(producer_namespace), btrim(managed_by) FROM svv_datashares WHERE share_type = 'INBOUND';
                
  share_name    | share_owner | source_database | consumer_database | share_type | is_publicaccessible | share_acl | producer_account |         producer_namespace           | managed_by 
----------------+-------------+-----------------+-------------------+------------+---------------------+-----------+------------------+--------------------------------------+------------
  salesshare    |             |                 |                   |  INBOUND   |       False         |           |  123456789012    | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | 
 marketingshare |             |                 |                   |  INBOUND   |       False         |           |  123456789012    | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d | ADX
```