# SESSION\_USER<a name="r_SESSION_USER"></a>

Returns the name of the user associated with the current session\. This is the user who initiated the current database connection\. 

**Note**  
Do not use trailing parentheses when calling SESSION\_USER\. 

## Syntax<a name="r_SESSION_USER-synopsis"></a>

```
session_user
```

## Return type<a name="r_SESSION_USER-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Example<a name="r_SESSION_USER-example"></a>

The following example returns the current session user: 

```
select session_user;

session_user
--------------
dwuser
(1 row)
```