# Usage notes<a name="r_REVOKE-usage-notes"></a>

To revoke privileges from an object, you must meet one of the following criteria:
+ Be the object owner\.
+ Be a superuser\.
+ Have a grant privilege for that object and privilege\.

  For example, the following command enables the user HR both to perform SELECT commands on the employees table and to grant and revoke the same privilege for other users\.

  ```
  grant select on table employees to HR with grant option;
  ```

  HR can't revoke privileges for any operation other than SELECT, or on any other table than employees\. 

Superusers can access all objects regardless of GRANT and REVOKE commands that set object privileges\.

PUBLIC represents a group that always includes all users\. By default all members of PUBLIC have CREATE and USAGE privileges on the PUBLIC schema\. To restrict any user's permissions on the PUBLIC schema, you must first revoke all permissions from PUBLIC on the PUBLIC schema, then grant privileges to specific users or groups\. The following example controls table creation privileges in the PUBLIC schema\.

```
revoke create on schema public from public;
```

To revoke privileges from a Lake Formation table, the IAM role associated with the table's external schema must have permission to revoke privileges to the external table\. The following example creates an external schema with an associated IAM role `myGrantor`\. IAM role `myGrantor` has the permission to revoke permissions from others\. The REVOKE command uses the permission of the IAM role `myGrantor` that is associated with the external schema to revoke permission to the IAM role `myGrantee`\.

```
create external schema mySchema
from data catalog
database 'spectrum_db'
iam_role 'arn:aws:iam::123456789012:role/myGrantor'
create external database if not exists;
```

```
revoke select
on external table mySchema.mytable
from iam_role 'arn:aws:iam::123456789012:role/myGrantee';
```

**Note**  
If the IAM role also has the `ALL` permission in an AWS Glue Data Catalog that is enabled for Lake Formation, the `ALL` permission isn't revoked\. Only the `SELECT` permission is revoked\. You can view the Lake Formation permissions in the Lake Formation console\.

## Usage notes for revoking the ASSUMEROLE privilege<a name="r_REVOKE-usage-notes-assumerole"></a>

The following usage notes apply to revoking the ASSUMEROLE privilege in Amazon Redshift\. 

Only a database superuser can revoke the ASSUMEROLE privilege for users and groups\. A superuser always retains the ASSUMEROLE privilege\. 

To enable the use of the ASSUMEROLE privilege for users and groups, a superuser runs the following statement once on the cluster\. Before granting the ASSUMEROLE privilege to users and groups, a superuser must run the following statement once on the cluster\.  

```
revoke assumerole on all from public for all;
```