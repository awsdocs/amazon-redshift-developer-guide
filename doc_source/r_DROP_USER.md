# DROP USER<a name="r_DROP_USER"></a>

Drops a user from a database\. Multiple users can be dropped with a single DROP USER command\. You must be a database superuser to execute this command\.

## Syntax<a name="r_DROP_USER-synopsis"></a>

```
DROP USER [ IF EXISTS ] name [, ... ]
```

## Parameters<a name="r_DROP_USER-parameters"></a>

IF EXISTS  
Clause that indicates that if the specified user account doesn’t exist, the command should make no changes and return a message that the user account doesn't exist, rather than terminating with an error\.  
This clause is useful when scripting, so the script doesn’t fail if DROP USER runs against a nonexistent user account\.

 *name*   
Name of the user account to remove\. You can specify multiple user accounts, with a comma separating each account name from the next\.

## Usage notes<a name="r_DROP_USER-notes"></a>

You can't drop a user if the user owns any database object, such as a schema, database, table, or view, or if the user has any privileges on a database, table, column, or group\. If you attempt to drop such a user, you receive one of the following errors\.

```
ERROR: user "username" can't be dropped because the user owns some object [SQL State=55006]

ERROR: user "username" can't be dropped because the user has a privilege on some object [SQL State=55006]
```

**Note**  
Amazon Redshift checks only the current database before dropping a user\. DROP USER doesn't return an error if the user owns database objects or has any privileges on objects in another database\. If you drop a user that owns objects in another database, the owner for those objects is changed to 'unknown'\. 

If a user owns an object, first drop the object or change its ownership to another user before dropping the original user\. If the user has privileges for an object, first revoke the privileges before dropping the user\. The following example shows dropping an object, changing ownership, and revoking privileges before dropping the user\.

```
drop database dwdatabase;
alter schema dw owner to dwadmin; 
revoke all on table dwtable from dwuser;
drop user dwuser;
```

## Examples<a name="r_DROP_USER-examples"></a>

The following example drops a user account called danny:

```
drop user danny;
```

The following example drops two user accounts, danny and billybob:

```
drop user danny, billybob;
```

The following example drops the user account danny if it exists, or does nothing and returns a message if it doesn't

```
drop user if exists danny;
```