# CURRENT\_DATABASE<a name="r_CURRENT_DATABASE"></a>

Returns the name of the database where you are currently connected\. 

## Syntax<a name="r_CURRENT_DATABASE-synopsis"></a>

```
current_database()
```

## Return type<a name="r_CURRENT_DATABASE-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Example<a name="r_CURRENT_DATABASE-example"></a>

The following query returns the name of the current database: 

```
select current_database();

current_database
------------------
tickit
(1 row)
```