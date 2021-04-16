# CURRENT\_USER\_ID<a name="r_CURRENT_USER_ID"></a>

Returns the unique identifier for the Amazon Redshift user logged in to the current session\.

## Syntax<a name="r_CURRENT_USER_ID-synopsis"></a>

```
CURRENT_USER_ID
```

## Return type<a name="r_CURRENT_USER_ID-return-type"></a>

The CURRENT\_USER\_ID function returns an integer\. 

## Examples<a name="r_CURRENT_USER_ID-examples"></a>

The following example returns the user name and current user ID for this session: 

```
select user, current_user_id;

 current_user | current_user_id
--------------+-----------------
   dwuser     |               1
(1 row)
```