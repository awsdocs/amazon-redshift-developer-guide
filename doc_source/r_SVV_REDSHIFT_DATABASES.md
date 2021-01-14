# SVV\_REDSHIFT\_DATABASES<a name="r_SVV_REDSHIFT_DATABASES"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_ REDSHIFT\_DATABASES to view a list of all the databases that a user has access to\. This includes the databases on the cluster and the databases created from data shares provided by remote clusters\. 

SVV\_REDSHIFT\_DATABASES is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_REDSHIFT_DATABASES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_DATABASES.html)

## Sample query<a name="r_SVV_REDSHIFT_DATABASES-sample-query"></a>

The following example returns the output for SVV\_REDSHIFT\_DATABASES\.

```
select * from svv_redshift_databases;

 database_name | database_owner | database_type | database_acl | database_options
---------------+----------------+---------------+--------------+------------------
 prod          |              1 | local         |              |
 test          |              1 | local         |              |
```