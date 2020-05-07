# CURRENT\_USER<a name="r_CURRENT_USER"></a>

Returns the user name of the current "effective" user of the database, as applicable to checking permissions\. Usually, this user name will be the same as the session user; however, this can occasionally be changed by superusers\. 

**Note**  
Do not use trailing parentheses when calling CURRENT\_USER\. 

## Syntax<a name="r_CURRENT_USER-synopsis"></a>

```
current_user
```

## Return type<a name="r_CURRENT_USER-return-type"></a>

CURRENT\_USER returns a CHAR or VARCHAR string\. 

## Example<a name="r_CURRENT_USER-example"></a>

The following query returns the name of the current database user: 

```
select current_user;

current_user
--------------
dwuser
(1 row)
```