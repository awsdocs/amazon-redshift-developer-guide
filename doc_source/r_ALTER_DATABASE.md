# ALTER DATABASE<a name="r_ALTER_DATABASE"></a>

Changes the attributes of a database\.

## Syntax<a name="r_ALTER_DATABASE-synopsis"></a>

```
ALTER DATABASE database_name
[ RENAME TO new_name
| OWNER TO new_owner
| CONNECTION LIMIT { limit | UNLIMITED }
| COLLATE { CASE_SENSITIVE | CASE_INSENSITIVE } ]
[ ISOLATION LEVEL { SERIALIZABLE | SNAPSHOT } ]
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
New database owner\. The new owner must be an existing database user with write privileges\. For more information about user privileges, see [GRANT](r_GRANT.md)\.

CONNECTION LIMIT \{ *limit* \| UNLIMITED \}   
The maximum number of database connections users are permitted to have open concurrently\. The limit is not enforced for superusers\. Use the UNLIMITED keyword to permit the maximum number of concurrent connections\.  A limit on the number of connections for each user might also apply\. For more information, see [CREATE USER](r_CREATE_USER.md)\. The default is UNLIMITED\. To view current connections, query the [STV\_SESSIONS](r_STV_SESSIONS.md) system view\.  
If both user and database connection limits apply, an unused connection slot must be available that is within both limits when a user attempts to connect\.

COLLATE \{ CASE\_SENSITIVE \| CASE\_INSENSITIVE \}  
A clause that specifies whether string search or comparison is case\-sensitive or case\-insensitive\.   
You can change the case sensitivity of the current database which is empty\.  
You must have the privilege to the current database to change case sensitivity\. Superusers or database owners with the CREATE DATABASE privilege can also change database case sensitivity\.

ISOLATION LEVEL \{ SERIALIZABLE \| SNAPSHOT \}  
A clause that specifies the isolation level used when queries run against a database\.  
+ SERIALIZABLE isolation – provides full serializability for concurrent transactions\. For more information, see [Serializable isolation](c_serial_isolation.md)\. 
+ SNAPSHOT isolation – provides an isolation level with protection against update and delete conflicts\. 
For more information about isolation levels, see [CREATE DATABASE](r_CREATE_DATABASE.md)\.  
Consider the following items when altering the isolation level of a database:  
+ You must have the superuser or CREATE DATABASE privilege to the current database to change the database isolation level\.
+ You can't alter the isolation level of the `dev` database\. 
+ You can't alter the isolation level within a transaction block\.
+ The alter isolation level command fails if other users are connected to the database\.
+ The alter isolation level command can alter the isolation level settings of the current session\.

## Usage notes<a name="r_ALTER_DATABASE-usage-notes"></a>

ALTER DATABASE commands apply to subsequent sessions not current sessions\. You must reconnect to the altered database to see the effect of the change\.

## Examples<a name="r_ALTER_DATABASE-examples"></a>

The following example renames a database named TICKIT\_SANDBOX to TICKIT\_TEST: 

```
alter database tickit_sandbox rename to tickit_test;
```

The following example changes the owner of the TICKIT database \(the current database\) to DWUSER: 

```
alter database tickit owner to dwuser;
```

The following example changes the database case sensitivity of the sampledb database:

```
ALTER DATABASE sampledb COLLATE CASE_INSENSITIVE;
```

The following example alters a database named **sampledb** with SNAPSHOT isolation level\.

```
ALTER DATABASE sampledb ISOLATION LEVEL SNAPSHOT;
```