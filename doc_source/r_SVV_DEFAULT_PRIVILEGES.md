# SVV\_DEFAULT\_PRIVILEGES<a name="r_SVV_DEFAULT_PRIVILEGES"></a>

 Use SVV\_DEFAULT\_PRIVILEGES to view the default privileges that a user has access to in an Amazon Redshift cluster\. 

 SVV\_DEFAULT\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all the rows\. Regular users can only see default privileges granted to them\. 

## Table columns<a name="r_SVV_DEFAULT_PRIVILEGES-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DEFAULT_PRIVILEGES.html)

## Sample query<a name="r_SVV_DEFAULT_PRIVILEGES-sample-query"></a>

 The following example returns the output for SVV\_DEFAULT\_PRIVILEGES\. 

```
SELECT * from svv_default_privileges;

 schema_name |    object_type    | owner_id | owner_name | owner_type | privilege_type | grantee_id | grantee_name | grantee_type | admin_option
-------------+-------------------+--------- +------------+------------+----------------+------------+--------------+--------------+-------------+
   public    |     RELATION      |    106   |     u1     |    user    |     UPDATE     |     107    |      u2      |     user     |      f      |
   public    |     RELATION      |    106   |     u1     |    user    |     SELECT     |     107    |      u2      |     user     |      f      |
```