# FROM\_VARBYTE function<a name="r_FROM_VARBYTE"></a>

FROM\_VARBYTE converts a binary value to a character string in the specified format\. 

## Syntax<a name="r_FROM_VARBYTE-synopsis"></a>

```
from_varbyte(binary_value, format)
```

## Arguments<a name="r_FROM_VARBYTE-arguments"></a>

 *binary\_value*   
A binary value of data type VARBYTE\. 

 *format*   
The format of the returned character string\. Case insensitive valid values are `hex`, `binary`, `utf-8`, and `utf8`\. 

## Return type<a name="r_FROM_VARBYTE-return-type"></a>

VARCHAR

## Examples<a name="r_FROM_VARBYTE-examples"></a>

The following example converts the binary value `'ab'` to hexadecimal\. 

```
select from_varbyte('ab', 'hex');
               
from_varbyte
------------
 6162
```

The following example returns the binary representation of `'4d'`\. The binary representation of '4d' is the character string `01001101`\.

```
select from_varbyte(from_hex('4d'), 'binary');
               
from_varbyte
--------------
 01001101
```