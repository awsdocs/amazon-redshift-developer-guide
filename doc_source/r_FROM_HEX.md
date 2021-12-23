# FROM\_HEX function<a name="r_FROM_HEX"></a>

FROM\_HEX converts a hexadecimal to a binary value\. 

## Syntax<a name="r_FROM_HEX-synopsis"></a>

```
from_hex(hex_string)
```

## Arguments<a name="r_FROM_HEX-arguments"></a>

 *hex\_string*   
Hexadecimal string of data type VARCHAR or TEXT to be converted\. The format must be a literal value\. 

## Return type<a name="r_FROM_HEX-return-type"></a>

VARBYTE

## Examples<a name="r_FROM_HEX-examples"></a>

The following example converts the hexadecimal representation of `'6162'` to a binary value\. The result is automatically shown as the hexadecimal representation of the binary value\.

```
select from_hex('6162');
               
 from_hex
----------
 6162
```