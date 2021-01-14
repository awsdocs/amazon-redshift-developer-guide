# SVV\_DATASHARE\_CONSUMERS<a name="r_SVV_DATASHARE_CONSUMERS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_DATASHARE\_CONSUMERS to view a list of consumers for data share created on the cluster\. 

## Table columns<a name="r_SVV_DATASHARE_CONSUMERS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARE_CONSUMERS.html)

## Sample query<a name="r_SVV_DATASHARE_CONSUMERS-sample-query"></a>

The following example returns the output for SVV\_DATASHARE\_CONSUMERS\.

```
select * from svv_datashare_consumers;

 share_name | consumer_account |          consumer_namespace          |     share_date
------------+------------------+--------------------------------------+---------------------
   share1   |   123456789012   | a1234567-bcd8-9012-3e45-6f78ghi9jkl0 |     2020-10-27
(1 row)
```