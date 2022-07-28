# TO\_VARBYTE function<a name="r_TO_VARBYTE"></a>

TO\_VARBYTE converts a string in a specified format to a binary value\. 

## Syntax<a name="r_TO_VARBYTE-synopsis"></a>

```
to_varbyte(string, format)
```

## Arguments<a name="r_TO_VARBYTE-arguments"></a>

 *string*   
A character string\. 

 *format*   
The format of the input string\. Case insensitive valid values are `hex`, `binary`, `utf-8`, and `utf8`\. 

## Return type<a name="r_TO_VARBYTE-return-type"></a>

VARBYTE

## Examples<a name="r_TO_VARBYTE-examples"></a>

The following example converts the hex `6162` to a binary value\. The result is automatically shown as the hexadecimal representation of the binary value\.

```
select to_varbyte('6162', 'hex');
               
to_varbyte
------------
 6162
```

The following example returns the binary for of `4d`\. The binary representation of '4d' is `01001101`\.

```
select to_varbyte('01001101', 'binary');
               
to_varbyte
------------
 4d
```

The following example converts the string `'a'` in UTF\-8 to a binary value\. The result is automatically shown as the hexadecimal representation of the binary value\.

```
select to_varbyte('a', 'utf8');
               
to_varbyte
------------
 61
```

The following example converts the string `'4'` in hexadecimal to a binary value\. If the hexadecimal string length is an odd number, then a `0` is prepended to form a valid hexadecimal number\.

```
select to_varbyte('4', 'hex');
               
to_varbyte
------------
 04
```