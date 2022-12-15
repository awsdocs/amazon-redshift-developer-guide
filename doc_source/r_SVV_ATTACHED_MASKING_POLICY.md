# SVV\_ATTACHED\_MASKING\_POLICY<a name="r_SVV_ATTACHED_MASKING_POLICY"></a>

Use SVV\_ATTACHED\_MASKING\_POLICY to view all the relations and roles/users with policies attached on the currently connected database\. 

Only users with the [https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html](https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html) role can view SVV\_ATTACHED\_MASKING\_POLICY\. Regular users will see 0 rows\.

Only policies created on provisioned clusters appear in SVV\_MASKING\_POLICY\.

## Table columns<a name="r_SVV_ATTACHED_MASKING_POLICY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ATTACHED_MASKING_POLICY.html)

## Internal functions<a name="r_SVV_ATTACHED_MASKING_POLICY-internal-functions"></a>

SVV\_ATTACHED\_MASKING\_POLICY supports the following internal functions: 

### mask\_get\_policy\_for\_role\_on\_column<a name="r_SVV_ATTACHED_MASKING_POLICY-internal-functions-get-pol-role"></a>

Get the highest priority policy that applies to a given column/role pair\.

#### Syntax<a name="r_ATTACHED_MASKING_POLICY-internal-functions-get-pol-role-syntax"></a>

```
mask_get_policy_for_role_on_column 
                        (relschema, 
                        relname, 
                        colname, 
                        rolename);
```

#### Parameters<a name="r_ATTACHED_MASKING_POLICY-internal-functions-get-pol-role-parameters"></a>

 *relschema*   
The name of the schema the policy is in\.

 *relname*   
The name of the table the policy is in\.

 *colname*   
The name of the column the policy is attached to\.

 *rolename*   
The name of the role the policy is attached to\.

### mask\_get\_policy\_for\_user\_on\_column<a name="r_SVV_ATTACHED_MASKING_POLICY-internal-functions-get-pol-user"></a>

Get the highest priority policy that applies to a given column/user pair\.

#### Syntax<a name="r_ATTACHED_MASKING_POLICY-internal-functions-get-pol-user-syntax"></a>

```
mask_get_policy_for_user_on_column 
                        (relschema, 
                        relname, 
                        colname, 
                        username);
```

#### Parameters<a name="r_ATTACHED_MASKING_POLICY-internal-functions-get-pol-user-parameters"></a>

 *relschema*   
The name of the schema the policy is in\.

 *relname*   
The name of the table the policy is in\.

 *colname*   
The name of the column the policy is attached to\.

 *rolename*   
The name of the user the policy is attached to\.