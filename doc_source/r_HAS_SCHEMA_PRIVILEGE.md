# HAS\_SCHEMA\_PRIVILEGE<a name="r_HAS_SCHEMA_PRIVILEGE"></a>

Returns `true` if the user has the specified privilege for the specified schema\. For more information about privileges, see [GRANT](r_GRANT.md)\.

## Syntax<a name="r_HAS_SCHEMA_PRIVILEGE-synopsis"></a>

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\.

```
has_schema_privilege( [ user, ] schema, privilege)
```

## Arguments<a name="r_HAS_SCHEMA_PRIVILEGE-arguments"></a>

 *user*   
Name of the user to check for schema privileges\. Default is to check the current user\. 

 *schema*   
Schema associated with the privilege\. 

 *privilege*   
Privilege to check\. Valid values are:   
+ CREATE
+ USAGE

## Return type<a name="r_HAS_SCHEMA_PRIVILEGE-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Example<a name="r_HAS_SCHEMA_PRIVILEGE-example"></a>

The following query confirms that the GUEST user has the CREATE privilege on the PUBLIC schema: 

```
select has_schema_privilege('guest', 'public', 'create');

has_schema_privilege
----------------------
true
(1 row)
```