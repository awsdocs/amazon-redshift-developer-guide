# SVV\_DATASHARES<a name="r_SVV_DATASHARES"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_DATASHARES to view a list of data shares created on the cluster and data shares shared with the cluster\.\. 

## Table columns<a name="r_SVV_DATASHARES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARES.html)

## Sample query<a name="r_SVV_DATASHARES-sample-query"></a>

The following example returns the output for SVV\_DATASHARES\.

```
select * from svv_datashares;

  share_name  | share_owner | source_database | consumer_database | share_type |     createdate      | is_publicaccessible | share_acl | producer_account |          producer_namespace
--------------+-------------+-----------------+-------------------+------------+---------------------+---------------------+-----------+------------------+--------------------------------------
 ds2_cluster2 |           1 | dev             |                   | OUTBOUND   | 2020-10-30 22:00:18 | f                   |           |                  |
 ds1          |           0 |                 |                   | INBOUND    |                     | f                   |           | 294284061811     | 8c5c634e-68d0-4627-83f1-7f3b389414e8
(2 rows)
```