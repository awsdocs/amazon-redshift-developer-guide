# Managing the data sharing lifecycle in Amazon Redshift<a name="datashare-lifecycle"></a>

The lifecycle of a datashare object follows the same paradigm as for any Amazon Redshift object, from creation to deletion\. 

As part of this lifecycle, Amazon Redshift applies drop user checks for datashare objects\. You can't drop a user if the user owns any datashare objects or if the user has any privileges on datashare objects\. For more information, see [DROP USER](r_DROP_USER.md)\. 

If you attempt to drop such a user, you receive one of the following errors\.

```
ERROR: user "username" can't be dropped because the user owns some object  [SQL State =55006] ERROR: user "username" can't be dropped because the user has a privilege on some object  [SQL State =55006]
```