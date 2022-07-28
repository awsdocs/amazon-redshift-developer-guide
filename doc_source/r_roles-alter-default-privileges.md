# ALTER DEFAULT PRIVILEGES for RBAC<a name="r_roles-alter-default-privileges"></a>

Use the ALTER DEFAULT PRIVILEGES statement to define the default set of access permissions to be applied to objects that are created in the future by the specified user\. By default, users can change only their own default access permissions\. With RBAC, you can set the default access permissions for roles\. For more information, see the [ALTER DEFAULT PRIVILEGES](r_ALTER_DEFAULT_PRIVILEGES.md) command\.

RBAC enables you to assign database object permissions to roles, similarly to system permissions\. Then you can assign roles to users, authorize users with system and/or database permissions\.