# ALTER DATABASE<a name="r_ALTER_DATABASE"></a>

Changes the attributes of a database\.

## Syntax<a name="r_ALTER_DATABASE-synopsis"></a>

```
ALTER DATABASE database_name
| RENAME TO new_name 
| OWNER TO new_owner
| CONNECTION LIMIT { limit | UNLIMITED } ]
```

## Parameters<a name="r_ALTER_DATABASE-parameters"></a>

 *database\_name*   
Name of the database to alter\. Typically, you alter a database that you are not currently connected to; in any case, the changes take effect only in subsequent sessions\. You can change the owner of the current database, but you can't rename it:  

```
alter database tickit rename to newtickit;
ERROR:  current database may not be renamed
```

RENAME TO   
Renames the specified database\. For more information about valid names, see [Names and identifiers](r_names.md)\. You can't rename the dev, padb\_harvest, template0, or template1 databases, and you can't rename the current database\. Only the database owner or a [superuser](r_superusers.md#def_superusers) can rename a database; non\-superuser owners must also have the CREATEDB privilege\.

 *new\_name*   
New database name\.

OWNER TO   
Changes the owner of the specified database\. You can change the owner of the current database or some other database\. Only a superuser can change the owner\.

 *new\_owner*   
New database owner\. The new owner must be an existing database user with write privileges\. See [GRANT](r_GRANT.md) for more information about user privileges\.

CONNECTION LIMIT \{ *limit* \| UNLIMITED \}   
The maximum number of database connections users are permitted to have open concurrently\. The limit is not enforced for superusers\. Use the UNLIMITED keyword to permit the maximum number of concurrent connections\.  A limit on the number of connections for each user might also apply\. For more information, see [CREATE USER](r_CREATE_USER.md)\. The default is UNLIMITED\. To view current connections, query the [STV\_SESSIONS](r_STV_SESSIONS.md) system view\.  
If both user and database connection limits apply, an unused connection slot must be available that is within both limits when a user attempts to connect\.

## Usage notes<a name="r_ALTER_DATABASE-usage-notes"></a>

ALTER DATABASE commands apply to subsequent sessions not current sessions\. You need to reconnect to the altered database to see the effect of the change\.

## Examples<a name="r_ALTER_DATABASE-examples"></a>

The following example renames a database named TICKIT\_SANDBOX to TICKIT\_TEST: 

```
alter database tickit_sandbox rename to tickit_test;
```

The following example changes the owner of the TICKIT database \(the current database\) to DWUSER: 

```
alter database tickit owner to dwuser;
```