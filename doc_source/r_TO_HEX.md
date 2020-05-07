# TO\_HEX function<a name="r_TO_HEX"></a>

The TO\_HEX function converts a number to its equivalent hexadecimal value\. 

## Syntax<a name="r_TO_HEX-syntax"></a>

```
TO_HEX(string)
```

## Arguments<a name="r_TO_HEX-arguments"></a>

 *number*   
A number to convert to its hexadecimal value\. 

## Return type<a name="r_TO_HEX-return-type"></a>

The TO\_HEX function returns a hexadecimal value\. 

## Examples<a name="r_TO_HEX-examples"></a>

The following example shows the conversion of a number to its hexadecimal value: 

```
select to_hex(2147676847);
to_hex
----------
8002f2af
(1 row)
```