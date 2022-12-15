# SET SESSION AUTHORIZATION<a name="r_SET_SESSION_AUTHORIZATION"></a>

Sets the user name for the current session\.

You can use the SET SESSION AUTHORIZATION command, for example, to test database access by temporarily running a session or transaction as an unprivileged user\. You must be a database superuser to run this command\.

## Syntax<a name="r_SET_SESSION_AUTHORIZATION-synopsis"></a>

```
SET [ LOCAL ] SESSION AUTHORIZATION { user_name | DEFAULT }
```

## Parameters<a name="r_SET_SESSION_AUTHORIZATION-parameters"></a>

LOCAL  
Specifies that the setting is valid for the current transaction\. Omitting this parameter specifies that the setting is valid for the current session\.

 *user\_name*   
Name of the user to set\. The user name may be written as an identifier or a string literal\.

DEFAULT  
Sets the session user name to the default value\.

## Examples<a name="r_SET_SESSION_AUTHORIZATION-examples"></a>

The following example sets the user name for the current session to `dwuser`:

```
SET SESSION AUTHORIZATION 'dwuser';
```

The following example sets the user name for the current transaction to `dwuser`:

```
SET LOCAL SESSION AUTHORIZATION 'dwuser';
```

This example sets the user name for the current session to the default user name:

```
SET SESSION AUTHORIZATION DEFAULT;
```