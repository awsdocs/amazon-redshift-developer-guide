# Considerations for role usage in RBAC<a name="r_role-usage-notes"></a>

When working with RBAC roles, consider the following:
+ Amazon Redshift doesn't allow cycles of role authorizations\. You can't grant r1 to r2 and then grant r2 to r1\.
+ RBAC works for both native Amazon Redshift objects and Amazon Redshift Spectrum tables\.
+ As an Amazon Redshift administrator, you can turn on RBAC by upgrading your cluster to the latest maintenance patch to get started\. 
+ Only superusers and users with the CREATE ROLE system permission can create roles\.
+ Only superusers and role administrators can modify or drop roles\.
+ A role name can't be the same as a user name\.
+ A role name can't contain invalid characters, such as “:/\\n\.”
+ A role name can't be a reserved word, such as PUBLIC\.
+ The role name can't start with the reserved prefix for default roles, `sys:`\.
+ You can't drop a role that has the RESTRICT parameter when it is granted to another role\. The default setting is RESTRICT\. Amazon Redshift throws an error when you try to drop a role that has inherited another role\.
+ Users that don't have admin permissions on a role can't grant or revoke a role\.