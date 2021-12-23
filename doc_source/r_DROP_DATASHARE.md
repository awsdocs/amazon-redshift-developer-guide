# DROP DATASHARE<a name="r_DROP_DATASHARE"></a>

Drops a datashare\. This command isn't reversible\.

Only a superuser or the datashare owner can drop a datashare\.

## Syntax<a name="r_DROP_DATASHARE-synopsis"></a>

```
DROP DATASHARE datashare_name;
```

## Parameters<a name="r_DROP_DATASHARE-parameters"></a>

 *datashare\_name*   
The name of the datashare to be dropped\.

## DROP DATASHARE usage notes<a name="r_DROP_DATASHARE_usage"></a>

When using the DROP DATASHARE statement, consider the following:
+ In general, we recommend that you don't drop an AWS Data Exchange datashare \(preview\) using the DROP DATASHARE statement\. If you do, the AWS accounts that have access to the datashare lose access\. Performing this type of alteration can breach data product terms in AWS Data Exchange\.

  The following example shows an error when an AWS Data Exchange datashare is dropped\.

  ```
  DROP DATASHARE salesshare;
  ERROR:  Drop of ADX-managed datashare salesshare requires session variable datashare_break_glass_session_var to be set to value '620c871f890c49'
  ```

  To allow dropping an AWS Data Exchange datashare, set the following variable and run the DROP DATASHARE statement again\.

  ```
  SET datashare_break_glass_session_var to '620c871f890c49';
  ```

  ```
  DROP DATASHARE salesshare;
  ```

  In this case, Amazon Redshift generates a random one\-time value to set the session variable to allow DROP DATASHARE for an AWS Data Exchange datashare\.

## Examples<a name="r_DROP_DATASHARE-examples"></a>

The following example drops a datashare named `salesshare`\.

```
DROP DATASHARE salesshare;
```