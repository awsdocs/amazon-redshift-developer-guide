# DROP MASKING POLICY<a name="r_DROP_MASKING_POLICY"></a>

Drops a dynamic data masking policy from all databases\. You can't drop a masking policy that's still attached to one or more tables\. For more information on dynamic data masking, see [Dynamic data masking](t_ddm.md)\.

Superusers and users or roles that have the sys:secadmin role can drop a masking policy\.

## Syntax<a name="r_DROP_MASKING_POLICY-synopsis"></a>

```
DROP MASKING POLICY policy_name;
```

## Parameters<a name="r_DROP_MASKING_POLICY-parameters"></a>

 *policy\_name*   
The name of the masking policy to drop\.