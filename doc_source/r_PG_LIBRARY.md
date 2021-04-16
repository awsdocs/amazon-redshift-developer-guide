# PG\_LIBRARY<a name="r_PG_LIBRARY"></a>

Stores information about user\-defined libraries\.

PG\_LIBRARY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_PG_LIBRARY-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_LIBRARY.html)

## Example<a name="r_PG_LIBRARY-example"></a>

The following example returns information for user\-installed libraries\. 

```
select * from pg_library;

name       | language_oid | file_store_id | owner
-----------+--------------+---------------+------
f_urlparse |       108254 |          2000 |   100
```