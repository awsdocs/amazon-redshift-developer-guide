# DROP PROCEDURE<a name="r_DROP_PROCEDURE"></a>

Drops a procedure\. To drop a procedure, both the procedure name and input argument data types \(signature\), are required\. Optionally, you can include the full argument data types, including OUT arguments\. 

## Syntax<a name="r_DROP_PROCEDURE-synopsis"></a>

```
DROP PROCEDURE sp_name ( [ [ argname ] [ argmode ] argtype [, ...] ] )
```

## Parameters<a name="r_DROP_PROCEDURE-parameters"></a>

 *sp\_name*   
The name of the procedure to be removed\. 

 *argname*   
The name of an input argument\. DROP PROCEDURE ignores argument names, because only the argument data types are needed to determine the procedure's identity\. 

 *argmode*   
The mode of an argument, which can be IN, OUT, or INOUT\. OUT arguments are optional because they aren't used to identify a stored procedure\. 

 *argtype*   
The data type of the input argument\. For a list of the supported data types, see [Data types](c_Supported_data_types.md)\. 

## Examples<a name="r_DROP_PROCEDURE-examples"></a>

The following example drops a stored procedure named `quarterly_revenue`\.

```
DROP PROCEDURE quarterly_revenue(volume INOUT bigint, at_price IN numeric,result OUT int);
```