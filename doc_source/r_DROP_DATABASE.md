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

## DROP DATABASE usage notes<a name="r_DROP_DATABASE_usage"></a>

When using the DROP DATABASE statement, consider the following:
+ In general, we recommend that you don't drop a database that contains an AWS Data Exchange datashare using the DROP DATABASE statement\. If you do, the AWS accounts that have access to the datashare lose access\. Performing this type of alteration can breach data product terms in AWS Data Exchange\.

  The following example shows an error when a database that contains an AWS Data Exchange datashare is dropped\.

  ```
  DROP DATABASE test_db;
  ERROR:   Drop of database test_db that contains ADX-managed datashare(s) requires session variable datashare_break_glass_session_var to be set to value 'ce8d280c10ad41'
  ```

  To allow dropping the database, set the following variable and run the DROP DATABASE statement again\.

  ```
  SET datashare_break_glass_session_var to 'ce8d280c10ad41';
  ```

  ```
  DROP DATABASE test_db;
  ```

  In this case, Amazon Redshift generates a random one\-time value to set the session variable to allow DROP DATABASE for a database that contains an AWS Data Exchange datashare\.

## Examples<a name="r_DROP_DATABASE-examples"></a>

The following example drops a database named TICKIT\_TEST: 

```
drop database tickit_test;
```