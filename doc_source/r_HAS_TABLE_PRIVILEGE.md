# HAS\_TABLE\_PRIVILEGE<a name="r_HAS_TABLE_PRIVILEGE"></a>

Returns `true` if the user has the specified privilege for the specified table\. 

## Syntax<a name="r_HAS_TABLE_PRIVILEGE-synopsis"></a>

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\. For more information about privileges, see [GRANT](r_GRANT.md)\.

```
has_table_privilege( [ user, ] table, privilege)
```

## Arguments<a name="r_HAS_TABLE_PRIVILEGE-arguments"></a>

 *user*   
The name of the user to check for table privileges\. The default is to check the current user\. 

 *table*   
Table associated with the privilege\. 

 *privilege*   
Privilege to check\. Valid values are the following:   
+ SELECT
+ INSERT
+ UPDATE
+ DELETE
+ REFERENCES

## Return type<a name="r_HAS_TABLE_PRIVILEGE-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Examples<a name="r_HAS_TABLE_PRIVILEGE-examples"></a>

The following query finds that the GUEST user doesn't have SELECT privilege on the LISTING table\. 

```
select has_table_privilege('guest', 'listing', 'select');

has_table_privilege
---------------------
false
(1 row)
```

The following query lists table privileges, including select, insert, update, and delete, using output from the pg\_tables and pg\_user catalog tables\. This is a sample only\. You might have to specify a schema name and table names from your database\. For more information, see [Querying the catalog tables](c_join_PG.md)\.

```
SELECT 
     tablename
     ,usename
     ,HAS_TABLE_PRIVILEGE(users.usename, tablename, 'select') AS sel
     ,HAS_TABLE_PRIVILEGE(users.usename, tablename, 'insert') AS ins
     ,HAS_TABLE_PRIVILEGE(users.usename, tablename, 'update') AS upd
     ,HAS_TABLE_PRIVILEGE(users.usename, tablename, 'delete') AS del
FROM
(SELECT * from pg_tables
WHERE schemaname = 'public' and tablename in ('event','listing')) as tables
,(SELECT * FROM pg_user) AS users;

tablename | usename    | sel  | ins  | upd  | del
-----------------------------------------------------
event	    john         true   true   true   true	
event	    sally        false  false  false  false	
event	    elsa         false  false  false  false	
listing     john         true	true   true   true	
listing     sally        false  false  false  false	
listing     elsa         false  false  false  false
```

In the sample, the schema name in the WHERE clause limits returned records to tables in the *public* schema\. The query also contains a cross join\. For more information, see [JOIN examples](r_Join_examples.md)\.