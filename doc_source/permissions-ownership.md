# Managing permissions and ownership<a name="permissions-ownership"></a>

Similar to how Amazon Redshift manages other database objects, such as tables, views, or functions, Amazon Redshift binds model creation and use to access control mechanisms\. There are separate privileges for creating a model running prediction functions\. 

The following examples use two user groups, retention\_analyst\_grp \(model creator\) and marketing\_analyst\_grp \(model user\) to illustrate how Amazon Redshift manages access control\. The retention analyst creates machine learning models that other set of users can use through acquired privileges\. 

A superuser can GRANT USER or GROUP privilege to create machine learning models using the following statement\.

```
GRANT CREATE MODEL TO GROUP retention_analyst_grp;
```

Users or groups with this privilege can subsequently create a model in any schema in the cluster if a user has the usual CREATE privilege on the SCHEMA\. The machine learning model is part of the schema hierarchy in a similar way to tables, views, procedures, and user\-defined functions\. 

Assuming a schema demo\_ml already exists, grant the two user groups the permission on the schema as follows\.

```
GRANT CREATE, USAGE ON SCHEMA demo_ml TO GROUP retention_analyst_grp;
```

```
GRANT USAGE ON SCHEMA demo_ml TO GROUP marketing_analyst_grp;
```

You can enable other users to use the machine learning inference function by granting the EXECUTE privilege\. The following example uses the EXECUTE privilege to grant the marketing\_analyst\_grp GROUP the privilege to use the model\.

```
GRANT EXECUTE ON MODEL demo_ml.customer_churn_auto_model TO marketing_analyst_grp;
```

Use the REVOKE statement with CREATE MODEL and EXECUTE to revoke those privileges from users or groups\. For more information on permission control commands, see [GRANT](r_GRANT.md) and [REVOKE](r_REVOKE.md)\.