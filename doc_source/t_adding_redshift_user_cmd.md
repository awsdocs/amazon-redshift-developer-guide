# Step 2: Create a database user<a name="t_adding_redshift_user_cmd"></a>

By default, only the master user that you created when you launched the cluster has access to the initial database in the cluster\. To grant other users access, you must create one or more user accounts\. Database user accounts are global across all the databases in a cluster; they do not belong to individual databases\.

Use the CREATE USER command to create a new database user\. When you create a new user, you specify the name of the new user and a password\. A password is required\. It must have between 8 and 64 characters, and it must include at least one uppercase letter, one lowercase letter, and one numeral\.

For example, to create a user named **GUEST** with password **ABCd4321**, issue the following command:

```
create user guest password 'ABCd4321';
```

For information about other command options, see [CREATE USER](r_CREATE_USER.md) in the SQL Command Reference\.