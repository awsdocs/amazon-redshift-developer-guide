# Managing the data sharing lifecycle<a name="datashare-lifecycle"></a>

The lifecycle of a data share object follows the same paradigm as for any Amazon Redshift object, from creation to deletion\.  

Amazon Redshift also applies drop user checks for data share objects\. You can't drop a user if the user owns any data share objects or if the user has any privileges on data share objects\. For more information, see [DROP USER](r_DROP_USER.md)\. If you attempt to drop such a user, you receive one of the following errors\.

```
ERROR: user "username" can't be dropped because the user owns some object  [SQL State =55006] ERROR: user "username" can't be dropped because the user has a privilege on some object  [SQL State =55006]
```