# SVV\_DATASHARES<a name="r_SVV_DATASHARES"></a>

Use SVV\_DATASHARES to view a list of datashares created on the cluster and datashares shared with the cluster\. 

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