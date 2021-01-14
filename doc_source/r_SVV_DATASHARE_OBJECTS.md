# SVV\_DATASHARE\_OBJECTS<a name="r_SVV_DATASHARE_OBJECTS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_DATASHARE\_OBJECTS to view a list of objects in all data shares created on the cluster or shared with the cluster\. 

## Table columns<a name="r_SVV_DATASHARE_OBJECTS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARE_OBJECTS.html)

## Sample query<a name="r_SVV_DATASHARE_OBJECTS-sample-query"></a>

The following example returns the output for SVV\_DATASHARE\_OBJECTS\.

```
select * from svv_datashare_objects;

 share_type |  share_name   | object_type |  object_name   | producer_account |          producer_namespace
------------+---------------+-------------+----------------+------------------+--------------------------------------
 OUTBOUND   |    share1     |   schema    |    schema_0    |                  |
 OUTBOUND   |    share1     |   schema    |    schema_1    |                  |
 OUTBOUND   |    share1     |   table     | schema1.table1 |                  |
 OUTBOUND   |    share1     |   table     | schema1.table2 |                  |
 OUTBOUND   |    share1     |   table     | schema2.table2 |                  |
 INBOUND    |    share2     |   view      | schema1.view1  |   123456789012   | a1234567-bcd8-9012-3e45-6f78ghi9jkl0
 INBOUND    |    share2     |   view      | schema2.view2  |   123456789012   | a1234567-bcd8-9012-3e45-6f78ghi9jkl0
 INBOUND    |    share2     |  function   | schema1.func1  |   123456789012   | a1234567-bcd8-9012-3e45-6f78ghi9jkl0
 INBOUND    |    share2     |  function   | schema1.func2  |   123456789012   | a1234567-bcd8-9012-3e45-6f78ghi9jkl0
 INBOUND    |    share2     |  function   | schema2.func1  |   123456789012   | a1234567-bcd8-9012-3e45-6f78ghi9jkl0
(10 rows)
```