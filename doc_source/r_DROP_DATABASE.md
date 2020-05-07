# DROP DATABASE<a name="r_DROP_DATABASE"></a>

Drops a database\. 

You can't run DROP DATABASE within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_DROP_DATABASE-synopsis"></a>

```
DROP DATABASE database_name
```

## Parameters<a name="r_DROP_DATABASE-parameters"></a>

 *database\_name*   
Name of the database to be dropped\. You can't drop the dev, padb\_harvest, template0, or template1 databases, and you can't drop the current database\.  
To drop an external database, drop the external schema\. For more information, see [DROP SCHEMA](r_DROP_SCHEMA.md)\.

## Examples<a name="r_DROP_DATABASE-examples"></a>

The following example drops a database named TICKIT\_TEST: 

```
drop database tickit_test;
```