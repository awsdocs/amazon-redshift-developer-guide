# ATTACH MASKING POLICY<a name="r_ATTACH_MASKING_POLICY"></a>

Attaches an existing dynamic data masking policy to a column\. For more information on dynamic data masking, see [Dynamic data masking](t_ddm.md)\.

Superusers and users or roles that have the sys:secadmin role can attach a masking policy\.

## Syntax<a name="r_ATTACH_MASKING_POLICY-synopsis"></a>

```
ATTACH MASKING POLICY policy_name
   ON { table_name }
   ( output_columns_names ) [ USING ( input_column_names ) ]
   TO { user_name | ROLE role_name | PUBLIC }
   [ PRIORITY priority ];
```

## Parameters<a name="r_ATTACH_MASKING_POLICY-parameters"></a>

*policy\_name*   
The name of the masking policy to attach\.

 *table\_name*   
The name of the table to attach the masking policy to\.

*output\_column\_names*   
The names of the columns that the masking policy will apply to\.

*input\_column\_names*   
The names of the columns that the masking policy will take as input\. This parameter is optional\. If not specified, the masking policy uses the output\_column\_names as inputs\.

*user\_name*   
The name of the user to whom the masking policy will attach\. You can't attach two policies to the same column/user pair\. You can attach a policy to a user and another policy to the user's role\. In this case, the policy with the higher priority applies\.  
You can only set one of user\_name, role\_name, and PUBLIC in a single ATTACH MASKING POLICY command\. 

*role\_name*   
The name of the role to which the masking policy will attach\. You can't attach two policies to the same column/role pair\. You can attach a policy to a user and another policy to the user's role\. In this case, the policy with the higher priority applies\.  
You can only set one of user\_name, role\_name, and PUBLIC in a single ATTACH MASKING POLICY command\. 

*PUBLIC*   
Attaches the masking policy to all users accessing the table\. You must give other masking policies attached to specific column/user or column/role pairs a higher priority than the PUBLIC policy for them to apply\.  
You can only set one of user\_name, role\_name, and PUBLIC in a single ATTACH MASKING POLICY command\. 

*priority*   
The priority of the masking policy\. When multiple masking policies apply to a given user's query, the highest priority policy applies\.  
You can't apply a policy to a column with the same priority as another policy attached to that column, even if they're for different roles\. This field is optional\. If you don't specify a priority, the masking policy defaults to attaching with a priority of 0\.