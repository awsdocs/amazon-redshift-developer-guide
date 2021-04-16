# Security and privileges for stored procedures<a name="stored-procedure-security-and-privileges"></a>

By default, all users have permission to create a procedure\. To create a procedure, you must have USAGE permission on the language PL/pgSQL, which is granted to PUBLIC by default\. Only superusers and owners have the permission to call a procedure by default\. Superusers can run REVOKE USAGE on PL/pgSQL from a user if they want to prevent the user from creating a stored procedure\. 

To call a procedure, you must be granted EXECUTE permission on the procedure\. By default, EXECUTE permission for new procedures is granted to the procedure owner and superusers\. For more information, see [GRANT](r_GRANT.md)\. 

The user creating a procedure is the owner by default\. The owner has CREATE, DROP, and EXECUTE privileges on the procedure by default\. Superusers have all privileges\. 

The SECURITY attribute controls a procedure's privileges to access database objects\. When you create a stored procedure, you can set the SECURITY attribute to either DEFINER or INVOKER\. If you specify SECURITY INVOKER, the procedure uses the privileges of the user invoking the procedure\. If you specify SECURITY DEFINER, the procedure uses the privileges of the owner of the procedure\. INVOKER is the default\. 

Because a SECURITY DEFINER procedure runs with the privileges of the user that owns it, take care to ensure that the procedure can't be misused\. To ensure that SECURITY DEFINER procedures can't be misused, do the following:
+ Grant EXECUTE on SECURITY DEFINER procedures to specific users, and not to PUBLIC\.
+ Qualify all database objects that the procedure needs to access with the schema names\. For example, use `myschema.mytable` instead of just `mytable`\.
+ If you can't qualify an object name by its schema, set `search_path` when creating the procedure by using the SET option\. Set `search_path` to exclude any schemas that are writable by untrusted users\. This approach prevents any callers of this procedure from creating objects \(for example, tables or views\) that mask objects intended to be used by the procedure\. For more information about the SET option, see [CREATE PROCEDURE](r_CREATE_PROCEDURE.md)\. 

The following example sets `search_path` to `admin` to ensure that the `user_creds` table is accessed from the `admin` schema and not from public or any other schema in the caller's `search_path`\.

```
CREATE OR REPLACE PROCEDURE sp_get_credentials(userid int, o_creds OUT varchar)
AS $$
BEGIN
  SELECT creds INTO o_creds
  FROM user_creds
  WHERE user_id = $1;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER
-- Set a secure search_path
SET search_path = admin;
```