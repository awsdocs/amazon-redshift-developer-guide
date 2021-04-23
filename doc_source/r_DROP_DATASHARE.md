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

## Examples<a name="r_DROP_DATASHARE-examples"></a>

The following example drops a datashare named SalesShare\.

```
DROP DATASHARE SalesShare;
```