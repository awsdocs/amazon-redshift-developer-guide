# Groups<a name="r_Groups"></a>

Groups are collections of users who are all granted whatever privileges are associated with the group\. You can use groups to assign privileges by role\. For example, you can create different groups for sales, administration, and support and give the users in each group the appropriate access to the data they require for their work\. You can grant or revoke privileges at the group level, and those changes will apply to all members of the group, except for superusers\.

To view all user groups, query the PG\_GROUP system catalog table:

```
select * from pg_group;
```

## Creating, altering, and deleting groups<a name="r_Groups-creating-altering-and-deleting-groups"></a>

Only a superuser can create, alter, or drop groups\.

You can perform the following actions:
+ To create a group, use the [CREATE GROUP](r_CREATE_GROUP.md) command\.
+ To add users to or remove users from an existing group, use the [ALTER GROUP](r_ALTER_GROUP.md) command\.
+ To delete a group, use the [DROP GROUP](r_DROP_GROUP.md) command\. This command only drops the group, not its member users\.