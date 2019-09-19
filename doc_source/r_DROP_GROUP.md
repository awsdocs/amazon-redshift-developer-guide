# DROP GROUP<a name="r_DROP_GROUP"></a>

Deletes a user group\. This command isn't reversible\. This command doesn't delete the individual users in a group\.

See DROP USER to delete an individual user\.

## Syntax<a name="r_DROP_GROUP-synopsis"></a>

```
DROP GROUP name
```

## Parameter<a name="r_DROP_GROUP-parameter"></a>

 *name*   
Name of the user group to delete\.

## Example<a name="r_DROP_GROUP-example"></a>

The following example deletes the GUEST user group:

```
drop group guests;
```

You can't drop a group if the group has any privileges on an object\. If you attempt to drop such a group, you will receive the following error\.

```
ERROR: group "guest" can't be dropped because the group has a privilege on some object
```

If the group has privileges for an object, first revoke the privileges before dropping the group\. The following example revokes all privileges on all tables in the `public` schema from the `GUEST` user group, and then drops the group\. 

```
revoke all on all tables in schema public from group guest;  
drop group guests;
```