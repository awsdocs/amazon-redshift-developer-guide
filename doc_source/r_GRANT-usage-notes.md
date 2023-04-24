# Usage notes<a name="r_GRANT-usage-notes"></a>

To grant privileges on an object, you must meet one of the following criteria:
+ Be the object owner\.
+ Be a superuser\.
+ Have a grant privilege for that object and privilege\.

For example, the following command enables the user HR both to perform SELECT commands on the employees table and to grant and revoke the same privilege for other users\.

```
grant select on table employees to HR with grant option;
```

HR can't grant privileges for any operation other than SELECT, or on any other table than employees\. 

As another example, the following command enables the user HR both to perform ALTER commands on the employees table and to grant and revoke the same privilege for other users\.

```
grant ALTER on table employees to HR with grant option;
```

HR can't grant privileges for any operation other than ALTER, or on any other table than employees\. 

Having privileges granted on a view doesn't imply having privileges on the underlying tables\. Similarly, having privileges granted on a schema doesn't imply having privileges on the tables in the schema\. Instead, grant access to the underlying tables explicitly\.

To grant privileges to an AWS Lake Formation table, the IAM role associated with the table's external schema must have permission to grant privileges to the external table\. The following example creates an external schema with an associated IAM role `myGrantor`\. The IAM role `myGrantor` has the permission to grant permissions to others\. The GRANT command uses the permission of the IAM role `myGrantor` that is associated with the external schema to grant permission to the IAM role `myGrantee`\.

```
create external schema mySchema
from data catalog
database 'spectrum_db'
iam_role 'arn:aws:iam::123456789012:role/myGrantor'
create external database if not exists;
```

```
grant select
on external table mySchema.mytable
to iam_role 'arn:aws:iam::123456789012:role/myGrantee';
```

If you GRANT ALL privileges to an IAM role, individual privileges are granted in the related Lake Formation–enabled Data Catalog\. For example, the following GRANT ALL results in the granted individual privileges \(SELECT, ALTER, DROP, DELETE, and INSERT\) showing in the Lake Formation console\.

```
grant all
on external table mySchema.mytable
to iam_role 'arn:aws:iam::123456789012:role/myGrantee';
```

Superusers can access all objects regardless of GRANT and REVOKE commands that set object privileges\.

## Usage notes for column\-level access control<a name="r_GRANT-usage-notes-clp"></a>

The following usage notes apply to column\-level privileges on Amazon Redshift tables and views\. These notes describe tables; the same notes apply to views unless we explicitly note an exception\. 
+ For an Amazon Redshift table, you can grant only the SELECT and UPDATE privileges at the column level\. For an Amazon Redshift view, you can grant only the SELECT privilege at the column level\. 
+ The ALL keyword is a synonym for SELECT and UPDATE privileges combined when used in the context of a column\-level GRANT on a table\. 
+ If you don't have the SELECT privilege on all columns in a table, performing a SELECT \* operation returns only those columns that you have access to\.
+ SELECT \* doesn't expand to only accessible columns in the following cases:
  + You can't create a regular view with only accessible columns using SELECT \*\.
  + You can't create a materialized view with only accessible columns using SELECT \*\.
+ If you have SELECT or UPDATE privilege on a table or view and add a column, you still have the same privileges on the table or view and thus all its columns\. 
+ Only a table's owner or a superuser can grant column\-level privileges\. 
+ The WITH GRANT OPTION clause isn't supported for column\-level privileges\.
+ You can't hold the same privilege at both the table level and the column level\. For example, the user `data_scientist` can't have both SELECT privilege on the table `employee` and SELECT privilege on the column `employee.department`\. Consider the following results when granting the same privilege to a table and a column within the table:
  + If a user has a table\-level privilege on a table, then granting the same privilege at the column level has no effect\. 
  + If a user has a table\-level privilege on a table, then revoking the same privilege for one or more columns of the table returns an error\. Instead, revoke the privilege at the table level\. 
  + If a user has a column\-level privilege, then granting the same privilege at the table level returns an error\. 
  + If a user has a column\-level privilege, then revoking the same privilege at the table level revokes both column and table privileges for all columns on the table\. 
+ You can't grant column\-level privileges on late\-binding views\.
+ To create a materialized view, you must have table\-level SELECT privilege on the base tables\. Even if you have column\-level privileges on specific columns, you can't create a materialized view on only those columns\. However, you can grant SELECT privilege to columns of a materialized view, similar to regular views\. 
+ To look up grants of column\-level privileges, use the [PG\_ATTRIBUTE\_INFO](r_PG_ATTRIBUTE_INFO.md) view\. 

## Usage notes for granting the ASSUMEROLE permission<a name="r_GRANT-usage-notes-assumerole"></a>

The following usage notes apply to granting the ASSUMEROLE permission in Amazon Redshift\. 

You use the ASSUMEROLE permission to control IAM role access permissions for database users, roles, or groups on commands such as COPY, UNLOAD, EXTERNAL FUNCTION, or CREATE MODEL\. After you grant the ASSUMEROLE permission to a user, role, or group for an IAM role, the user, role, or group can assume that role when running the command\. The ASSUMEROLE permission enables you to grant access to the appropriate commands as required\.

Only a database superuser can grant or revoke the ASSUMEROLE permission for users, roles, and groups\. A superuser always retains the ASSUMEROLE permission\.

To enable the use of the ASSUMEROLE permission for users, roles, and groups, a superuser performs the following two actions:
+ Run the following statement once on the cluster:

  ```
  revoke assumerole on all from public for all;
  ```
+ Grant the ASSUMEROLE permission to users, roles, and groups for the appropriate commands\.

You can specify role chaining in the ON clause when granting the ASSUMEROLE permission\. You use commas to separate roles in a role chain, for example, `Role1,Role2,Role3`\. If role chaining was specified when granting the ASSUMEROLE permission, you must specify the role chain when performing operations granted by the ASSUMEROLE permission\. You can't specify individual roles within the role chain when performing operations granted by the ASSUMEROLE permission\. For example, if a user, role, or group is granted the role chain `Role1,Role2,Role3`, you can't specify only `Role1` to perform operations\. 

If a user attempts to perform a COPY, UNLOAD, EXTERNAL FUNCTION, or CREATE MODEL operation and hasn't been granted the ASSUMEROLE permission, a message similar to the following appears\.

```
ERROR:  User awsuser does not have ASSUMEROLE permission on IAM role "arn:aws:iam::123456789012:role/RoleA" for COPY 
```

To list users that have been granted access to IAM roles and commands through the ASSUMEROLE permission, see [HAS\_ASSUMEROLE\_PRIVILEGE](r_HAS_ASSUMEROLE_PRIVILEGE.md)\. To list IAM roles and command permissions that have been granted to a user that you specify, see [PG\_GET\_IAM\_ROLE\_BY\_USER](PG_GET_IAM_ROLE_BY_USER.md)\. To list users, roles, and groups that have been granted access to an IAM role that you specify, see [PG\_GET\_GRANTEE\_BY\_IAM\_ROLE](PG_GET_GRANTEE_BY_IAMROLE.md)\.

## Usage notes for granting machine learning permissions<a name="r_GRANT-usage-notes-create-model"></a>

You can't directly grant or revoke permissions related to an ML function\. An ML function belongs to an ML model and permissions are controlled through the model\. Instead, you can grant permissions related to the ML model\. The following example demonstrates how to grant permisisons to all users to run the ML function associated with the model `customer_churn`\.

```
GRANT EXECUTE ON MODEL customer_churn TO PUBLIC;
```

You can also grant all permissions to to a user for the ML model `customer_churn`\.

```
GRANT ALL on MODEL customer_churn TO ml_user;
```

Granting the `EXECUTE` permission related to an ML function will fail if there is an ML function in the schema, even if that ML function already has the `EXECUTE` permission through `GRANT EXECUTE ON MODEL`\. We recommend using a separate schema when using the `CREATE MODEL` command to keep the ML functions in a separate schema by themselves\. The following example demonstrates how to do so\.

```
CREATE MODEL ml_schema.customer_churn
FROM customer_data
TARGET churn
FUNCTION ml_schema.customer_churn_prediction
IAM_ROLE default
SETTINGS (
 S3_BUCKET 'your-s3-bucket'
);
```