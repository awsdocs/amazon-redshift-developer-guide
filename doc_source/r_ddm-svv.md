# System views for dynamic data masking<a name="r_ddm-svv"></a>

You can access the following DDM related system views with the [https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html](https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html) role:
+  [SVV\_MASKING\_POLICY](r_SVV_MASKING_POLICY.md) 

   Use the SVV\_MASKING\_POLICY to view all masking policies created on the cluster\. 
+  [SVV\_ATTACHED\_MASKING\_POLICY](r_SVV_ATTACHED_MASKING_POLICY.md) 

  Use the SVV\_ATTACHED\_MASKING\_POLICY to view all the relations and roles/users with policies attached on the currently connected database\.

Only policies created on provisioned clusters appear in these system views\.

Following are some examples of the information that you can find using system views\.

```
--Select all policies associated with specific users, as opposed to roles
SELECT policy_name,
       schema_name,
       table_name,
       grantee
FROM svv_attached_masking_policy
WHERE grantee_type = 'user';     

--Select all policies attached to a specific user
SELECT policy_name,
       schema_name,
       table_name,
       grantee
FROM svv_attached_masking_policy
WHERE grantee = 'target_grantee_name'            
            
--Select all policies attached to a given table
SELECT policy_name,
       schema_name,
       table_name,
       grantee
FROM svv_attached_masking_policy
WHERE table_name = 'target_table_name'
      AND schema_name = 'target_schema_name';            
            
--Select the highest priority policy attachment for a given role
SELECT samp.policy_name,
       samp.priority,
       samp.grantee,
       smp.policy_expression
FROM svv_masking_policy AS smp
JOIN svv_attached_masking_policy AS samp
    ON samp.policy_name = smp.policy_name
WHERE
    samp.grantee_type = 'role' AND
    samp.policy_name = mask_get_policy_for_role_on_column(
        'target_schema_name', 
        'target_table_name', 
        'target_column_name', 
        'target_role_name')
ORDER BY samp.priority desc
LIMIT 1;         

--See which policy a specific user will see on a specific column in a given relation
SELECT samp.policy_name,
       samp.priority,
       samp.grantee,
       smp.policy_expression
FROM svv_masking_policy AS smp
JOIN svv_attached_masking_policy AS samp
    ON samp.policy_name = smp.policy_name
WHERE
    samp.grantee_type = 'role' AND
    samp.policy_name = mask_get_policy_for_user_on_column(
        'target_schema_name',
        'target_table_name',
        'target_column_name',
        'target_user_name')
ORDER BY samp.priority desc;
```