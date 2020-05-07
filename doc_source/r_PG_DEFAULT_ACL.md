# PG\_DEFAULT\_ACL<a name="r_PG_DEFAULT_ACL"></a>

Stores information about default access privileges\. For more information on default access privileges, see [ALTER DEFAULT PRIVILEGES](r_ALTER_DEFAULT_PRIVILEGES.md)\.

PG\_DEFAULT\_ACL is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_PG_DEFAULT_ACL-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_DEFAULT_ACL.html)

## Example<a name="r_PG_DEFAULT_ACL-example"></a>

The following query returns all default privileges defined for the database\. 

```
select pg_get_userbyid(d.defacluser) as user, 
n.nspname as schema, 
case d.defaclobjtype when 'r' then 'tables' when 'f' then 'functions' end 
as object_type, 
array_to_string(d.defaclacl, ' + ')  as default_privileges 
from pg_catalog.pg_default_acl d 
left join pg_catalog.pg_namespace n on n.oid = d.defaclnamespace;

 user  | schema | object_type |              default_privileges
-------+--------+-------------+-------------------------------------------------------
 admin | tickit | tables      | user1=r/admin + "group group1=a/admin" + user2=w/admin
```

The result in the preceding example shows that for all new tables created by user `admin` in the `tickit` schema, `admin` grants SELECT privileges to `user1`, INSERT privileges to `group1`, and UPDATE privileges to `user2`\.