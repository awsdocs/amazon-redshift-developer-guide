# Database object permissions<a name="r_roles-database-privileges"></a>

Apart from system permissions, Amazon Redshift includes database object permissions that define access options\. These include such options as the ability to read data in tables and views, write data, create tables, and drop tables\. For more information, see [GRANT](r_GRANT.md) command\.

By using RBAC, you can assign database object permissions to roles, similarly to how you can with system permissions\. Then you can assign roles to users, authorize users with system permissions, and authorize users with database permissions\.