# Downgrading or restoring to an Amazon Redshift cluster version that doesn't support RLS<a name="t_rls_downgrade"></a>

Once a relation is protected with an RLS policy, it is only accessible by authorized users or roles\. When a cluster is downgraded to an Amazon Redshift cluster version where RLS is unsupported or unavailable, only superusers can access RLS\-protected relations\.

When you perform a restore from the current track that supports RLS to a trailing track that doesn't support RLS, only superusers can access the protected relations\.

We recommend that you upgrade your cluster to a version that supports RLS to use all the RLS functionalities\. When RLS is unavailable, Amazon Redshift can't enforce consistency of policy\-dependent objects\. Avoid making any schema changes until RLS becomes available\.

While RLS is unavailable, superusers can temporarily restore access to other users who have the sys:secadmin system role, or the system permission IGNORE RLS\. Superusers can first revoke the SELECT permission from all users or roles that don't have the sys:secadmin or system permission IGNORE RLS\. Subsequently, superusers can turn off RLS with ALTER TABLE\. 