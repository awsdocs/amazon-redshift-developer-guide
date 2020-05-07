# CREATE GROUP<a name="r_CREATE_GROUP"></a>

Defines a new user group\. Only a superuser can create a group\.

## Syntax<a name="r_CREATE_GROUP-synopsis"></a>

```
CREATE GROUP group_name
[ [ WITH ] [ USER username ] [, ...] ]
```

## Parameters<a name="r_CREATE_GROUP-parameters"></a>

 *group\_name*   
Name of the new user group\. Group names beginning with two underscores are reserved for Amazon Redshift internal use\. For more information about valid names, see [Names and identifiers](r_names.md)\.

WITH  
Optional syntax to indicate additional parameters for CREATE GROUP\.

USER  
Add one or more users to the group\.

 *username*   
Name of the user to add to the group\.

## Examples<a name="r_CREATE_GROUP-examples"></a>

The following example creates a user group named ADMIN\_GROUP with a two users, ADMIN1 and ADMIN2\.

```
create group admin_group with user admin1, admin2;
```