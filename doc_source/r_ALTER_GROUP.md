# ALTER GROUP<a name="r_ALTER_GROUP"></a>

Changes a user group\. Use this command to add users to the group, drop users from the group, or rename the group\. 

## Syntax<a name="r_ALTER_GROUP-synopsis"></a>

```
ALTER GROUP group_name
{
ADD USER username [, ... ] |
DROP USER username [, ... ] |
RENAME TO new_name
}
```

## Parameters<a name="r_ALTER_GROUP-parameters"></a>

 *group\_name*   
Name of the user group to modify\. 

ADD   
Adds a user to a user group\. 

DROP   
Removes a user from a user group\. 

 *username*   
Name of the user to add to the group or drop from the group\. 

RENAME TO   
Renames the user group\. Group names beginning with two underscores are reserved for Amazon Redshift internal use\. For more information about valid names, see [Names and identifiers](r_names.md)\. 

 *new\_name*   
New name of the user group\. 

## Examples<a name="r_ALTER_GROUP-examples"></a>

The following example adds a user named DWUSER to the ADMIN\_GROUP group:

```
alter group admin_group
add user dwuser;
```

The following example renames the group ADMIN\_GROUP to ADMINISTRATORS: 

```
alter group admin_group
rename to administrators;
```