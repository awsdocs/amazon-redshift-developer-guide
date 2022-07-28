# SVV\_REDSHIFT\_DATABASES<a name="r_SVV_REDSHIFT_DATABASES"></a>

Use SVV\_ REDSHIFT\_DATABASES to view a list of all the databases that a user has access to\. This includes the databases on the cluster and the databases created from datashares provided by remote clusters\. 

SVV\_REDSHIFT\_DATABASES is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_REDSHIFT_DATABASES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_DATABASES.html)

## Sample query<a name="r_SVV_REDSHIFT_DATABASES-sample-query"></a>

The following example returns the output for SVV\_REDSHIFT\_DATABASES\.

```
SELECT database_name, database_type
FROM svv_redshift_databases
WHERE database_name = 'tickit_db';

database_name | database_owner | database_type | database_acl | database_options
--------------+----------------+---------------+--------------+------------------
   tickit_db  |     shared     |               |              |
```