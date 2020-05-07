# Delete a database user<a name="t_deleting_redshift_user_cmd"></a>

You won't need the GUEST user account for this tutorial, so you can delete it\. If you delete a database user account, the user will no longer be able to access any of the cluster databases\.

Issue the following command to drop the GUEST user:

```
drop user guest;
```

The master user you created when you launched your cluster continues to have access to the database\.

**Important**  
Amazon Redshift strongly recommends that you do not delete the master user\.

For information about command options, see [DROP USER](r_DROP_USER.md) in the SQL Reference\.