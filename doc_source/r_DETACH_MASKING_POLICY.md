# DETACH MASKING POLICY<a name="r_DETACH_MASKING_POLICY"></a>

Detaches an already attached dynamic data masking policy from a column\. For more information on dynamic data masking, see [Dynamic data masking \(preview\)](t_ddm.md)\.

Superusers and users or roles that have the sys:secadmin role can detach a masking policy\.

## Syntax<a name="r_DETACH_MASKING_POLICY-synopsis"></a>

```
DETACH MASKING POLICY policy_name
   ON { table_name }
   ( output_column_names )
   FROM { user_name | ROLE role_name | PUBLIC };
```

## Parameters<a name="r_DETACH_MASKING_POLICY-parameters"></a>

 *policy\_name*   
The name of the masking policy to detach\.

 *table\_name*   
The name of the table to detach the masking policy from\.

*output\_column\_names*   
The names of the columns to which the masking policy was attached\.

*user\_name*   
The name of the user to whom the masking policy was attached\.  
You can only set one of user\_name, role\_name, and PUBLIC in a single DETACH MASKING POLICY statement\.

*role\_name*   
The name of the role to which the masking policy was attached\.  
You can only set one of user\_name, role\_name, and PUBLIC in a single DETACH MASKING POLICY statement\.

*PUBLIC*   
Shows that the policy was attached to all users in the table\.  
You can only set one of user\_name, role\_name, and PUBLIC in a single DETACH MASKING POLICY statement\.