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
Name of the user to check for table privileges\. The default is to check the current user\. 

 *table*   
Table associated with the privilege\. 

 *privilege*   
Privilege to check\. Valid values are:   
+ SELECT
+ INSERT
+ UPDATE
+ DELETE
+ REFERENCES

## Return type<a name="r_HAS_TABLE_PRIVILEGE-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Examples<a name="r_HAS_TABLE_PRIVILEGE-examples"></a>

The following query finds that the GUEST user does not have SELECT privilege on the LISTING table: 

```
select has_table_privilege('guest', 'listing', 'select');

has_table_privilege
---------------------
false
(1 row)
```