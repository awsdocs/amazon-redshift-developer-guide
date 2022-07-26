# SVV\_ML\_MODEL\_PRIVILEGES<a name="r_SVV_ML_MODEL_PRIVILEGES"></a>

Use SVV\_ML\_MODEL\_PRIVILEGES to view the machine learning model permissions that are explicitly granted to users, roles, and groups in the cluster\.

SVV\_ML\_MODEL\_PRIVILEGES is visible to all users\. Superusers can see all rows\. Regular users who have the ACCESS SYSTEM TABLE permission can see all rows\. Regular users can only see identities to which they have access or for which they are the identities owner\.

## Table columns<a name="r_SVV_ML_MODEL_PRIVILEGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ML_MODEL_PRIVILEGES.html)

## Sample query<a name="r_SVV_ML_MODEL_PRIVILEGES-sample-query"></a>

The following example displays the result of the SVV\_ML\_MODEL\_PRIVILEGES\.

```
SELECT namespace_name,model_name,model_version,privilege_type,identity_name,identity_type,admin_option FROM svv_ml_model_privileges
WHERE model_name = 'test_model';

 namespace_name | model_name | model_version | privilege_type |  identity_name | identity_type | admin_option
----------------+------------+---------------+----------------+----------------+---------------+--------------
      public    | test_model |       1       |    EXECUTE     |     reguser    |     user      |    False
      public    | test_model |       1       |    EXECUTE     |     role1      |     role      |    False
```