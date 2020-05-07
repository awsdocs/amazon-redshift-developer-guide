# Default database user privileges<a name="r_Privileges"></a>

When you create a database object, you are its owner\. By default, only a superuser or the owner of an object can query, modify, or grant privileges on the object\. For users to use an object, you must grant the necessary privileges to the user or the group that contains the user\. Database superusers have the same privileges as database owners\.

Amazon Redshift supports the following privileges: SELECT, INSERT, UPDATE, DELETE, REFERENCES, CREATE, TEMPORARY, and USAGE\. Different privileges are associated with different object types\. For information on database object privileges supported by Amazon Redshift, see the [GRANT](r_GRANT.md) command\.

The right to modify or destroy an object is always the privilege of the owner only\. 

To revoke a privilege that was previously granted, use the [REVOKE](r_REVOKE.md) command\. The privileges of the object owner, such as DROP, GRANT, and REVOKE privileges, are implicit and cannot be granted or revoked\. Object owners can revoke their own ordinary privileges, for example, to make a table read\-only for themselves as well as others\. Superusers retain all privileges regardless of GRANT and REVOKE commands\.