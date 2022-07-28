# SVV\_COLUMN\_PRIVILEGES<a name="r_SVV_COLUMN_PRIVILEGES"></a>

Use SVV\_COLUMN\_PRIVILEGES to view the column permissions that are explicitly granted to users, roles, and groups in the current database\.

SVV\_COLUMN\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_COLUMN_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_COLUMN_PRIVILEGES.html)

## Sample query<a name="r_SVV_COLUMN_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_COLUMN\_PRIVILEGES\.

```
SELECT namespace_name,relation_name,COLUMN_NAME,privilege_type,identity_name,identity_type
FROM svv_column_privileges WHERE relation_name = 'lineitem';

 namespace_name | relation_name | column_name | privilege_type | identity_name | identity_type
----------------+---------------+-------------+----------------+---------------+----------------
    public      |   lineitem    | l_orderkey  |     SELECT     |    reguser    |     user
    public      |   lineitem    | l_orderkey  |     SELECT     |     role1     |     role
    public      |   lineitem    | l_partkey   |     SELECT     |    reguser    |     user
    public      |   lineitem    | l_partkey   |     SELECT     |     role1     |     role
```