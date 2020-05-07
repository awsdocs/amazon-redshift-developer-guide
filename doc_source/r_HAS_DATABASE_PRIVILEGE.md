# HAS\_DATABASE\_PRIVILEGE<a name="r_HAS_DATABASE_PRIVILEGE"></a>

Returns `true` if the user has the specified privilege for the specified database\. For more information about privileges, see [GRANT](r_GRANT.md)\. 

## Syntax<a name="r_HAS_DATABASE_PRIVILEGE-synopsis"></a>

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\.

```
has_database_privilege( [ user, ] database, privilege)
```

## Arguments<a name="r_HAS_DATABASE_PRIVILEGE-arguments"></a>

 *user*   
Name of the user to check for database privileges\. Default is to check the current user\. 

 *database*   
Database associated with the privilege\. 

 *privilege*   
Privilege to check\. Valid values are:   
+ CREATE
+ TEMPORARY
+ TEMP

## Return type<a name="r_HAS_DATABASE_PRIVILEGE-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Example<a name="r_HAS_DATABASE_PRIVILEGE-example"></a>

The following query confirms that the GUEST user has the TEMP privilege on the TICKIT database: 

```
select has_database_privilege('guest', 'tickit', 'temp');

has_database_privilege
------------------------
true
(1 row)
```