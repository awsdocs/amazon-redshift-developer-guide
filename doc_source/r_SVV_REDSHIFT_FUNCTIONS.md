# SVV\_REDSHIFT\_FUNCTIONS<a name="r_SVV_REDSHIFT_FUNCTIONS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_REDSHIFT\_FUNCTIONS to view a list of all functions that a user has access to\. This includes the functions on the cluster and the functions from data shares provided by remote clusters\.

## Table columns<a name="r_SVV_REDSHIFT_FUNCTIONS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_FUNCTIONS.html)

## Sample query<a name="r_SVV_REDSHIFT_FUNCTIONS-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_FUNCTIONS\.

```
select * from svv_redshift_functions;

 database_name | schema_name | function_name | function_type |      argument_type                 |  result_type   
---------------+-------------+---------------+---------------+------------------------------------+------------------
 dev           | public      | f_py_greater  | functions     | double precision, double precision | double precision
(1 row)
```