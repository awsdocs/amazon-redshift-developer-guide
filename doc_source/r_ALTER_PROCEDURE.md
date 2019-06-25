# ALTER PROCEDURE<a name="r_ALTER_PROCEDURE"></a>

Renames a procedure or changes the owner\. Both the procedure name and data types, or signature, are required\. Only the owner or a superuser can rename a procedure\. Only a superuser can change the owner of a procedure\. 

## Syntax<a name="r_ALTER_PROCEDURE-synopsis"></a>

```
ALTER PROCEDURE sp_name [ ( [ [ argname ] [ argmode ] argtype [, ...] ] ) ]
    RENAME TO new_name
```

```
ALTER PROCEDURE sp_name [ ( [ [ argname ] [ argmode ] argtype [, ...] ] ) ]
    OWNER TO { new_owner | CURRENT_USER | SESSION_USER }
```

## Parameters<a name="r_ALTER_PROCEDURE-parameters"></a>

 *sp\_name*   
The name of the procedure to be altered\. Either specify just the name of the procedure in the current search path, or use the format `schema_name.sp_procedure_name` to use a specific schema\.

*\[argname\] \[ argmode\] argtype*   
A list of argument names, argument modes, and data types\. Only the input data types are required, which are used to identify the stored procedure\. Alternatively, you can provide the full signature used to create the procedure including the input and output parameters with their modes\.

 *new\_name*   
A new name for the stored procedure\. 

*new\_owner* \| CURRENT\_USER \| SESSION\_USER  
A new owner for the stored procedure\. 

## Examples<a name="r_ALTER_PROCEDURE-examples"></a>

The following example changes the name of a procedure from `first_quarter_revenue` to `quarterly_revenue`\.

```
ALTER PROCEDURE first_quarter_revenue(volume INOUT bigint, at_price IN numeric, 
 result OUT int) RENAME TO quarterly_revenue;
```

This example is equivalent to the following\.

```
ALTER PROCEDURE first_quarter_revenue(bigint, numeric) RENAME TO quarterly_revenue;
```

The following example changes the owner of a procedure to `etl_user`\.

```
ALTER PROCEDURE quarterly_revenue(bigint, numeric) OWNER TO etl_user;
```