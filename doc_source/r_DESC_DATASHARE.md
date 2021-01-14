# DESC DATASHARE<a name="r_DESC_DATASHARE"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Displays the list of database objects within a data share that are added to it using ALTER DATASHARE\. Amazon Redshift displays the names, databases, schemas, and types of tables, views, and functions\. 

## Syntax<a name="r_DESC_DATASHARE-synopsis"></a>

```
DESC DATASHARE datashare_name [ OF NAMESPACE namespace_guid ]
```

## Parameters<a name="r_DESC_DATASHARE-parameters"></a>

 *datashare\_name*   
The name of the data share that the external database is created upon\.

NAMESPACE *namespace\_guid*   
A value that specifies the namespace that the data share uses\. When you run DESC DATAHSARE as a consumer cluster administrator, you must specify the NAMESPACE option to view inbound data shares\.

## Examples<a name="r_DESC_DATASHARE-examples"></a>

The following example displays the information of outbound data shares on a producer cluster\.

```
DESC DATASHARE SalesShare;
          
producer_account | producer_namespace                      | share_type  | share_name   | object_type | object_name 
-----------------+-----------------------+-------------+-------------+-------------+---------------------------------------------------
'467896856988'   | 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b'  | OUTBOUND    | 'SalesShare' | TABLE       | 'public.ticket_venue_redshift'
'467896856988'   | 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b'  | OUTBOUND    | 'SalesShare' | SCHEMA      | 'public'
```

The following example displays the information of inbound data shares on a consumer cluster\.

```
DESC DATASHARE SalesShare of namespace 'dd8772e1-d792-4fa4-996b-1870577efc0d';
            
 producer_account |          producer_namespace          | share_type | share_name | object_type |         object_name
------------------+--------------------------------------+------------+------------+-------------+------------------------------
 467896856988     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | table       | public.tickit_venue_redshift
 467896856988     | dd8772e1-d792-4fa4-996b-1870577efc0d | INBOUND    | salesshare | schema      | public
(2 rows)
```