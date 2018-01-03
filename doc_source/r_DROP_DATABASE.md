# DROP DATABASE<a name="r_DROP_DATABASE"></a>

Drops a database\. 

## Syntax<a name="r_DROP_DATABASE-synopsis"></a>

```
DROP DATABASE database_name
```

## Parameters<a name="r_DROP_DATABASE-parameters"></a>

 *database\_name*   
Name of the database to be dropped\. You cannot drop the dev, padb\_harvest, template0, or template1 databases, and you cannot drop the current database\.

## Examples<a name="r_DROP_DATABASE-examples"></a>

The following example drops a database named TICKIT\_TEST: 

```
drop database tickit_test;
```