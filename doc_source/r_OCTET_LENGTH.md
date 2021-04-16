# OCTET\_LENGTH function<a name="r_OCTET_LENGTH"></a>

Returns the length of the specified string as the number of bytes\. 

## Syntax<a name="r_OCTET_LENGTH-synopsis"></a>

```
OCTET_LENGTH(expression)
```

## Argument<a name="r_OCTET_LENGTH-argument"></a>

 *expression*   
The input parameter is a CHAR or VARCHAR text string\. 

## Return type<a name="r_OCTET_LENGTH-return-type"></a>

The OCTET\_LENGTH function returns an integer indicating the number of bytes in the input string\. The [LEN](r_LEN.md) function returns the actual number of characters in multi\-byte strings, not the number of bytes\. For example, to store three four\-byte Chinese characters, you need a VARCHAR\(12\) column\. The LEN function will return 3 for that same string\.

## Usage notes<a name="r_OCTET_LENGTH_usage_notes"></a>

Length calculations do not count trailing spaces for fixed\-length character strings but do count them for variable\-length strings\. 

## Example<a name="r_OCTET_LENGTH-example"></a>

The following example returns the number of bytes and the number of characters in the string `français`\.

```
select octet_length('français'), 
len('français');

octet_length  | len
--------------+-----
           9  |   8
(1 row)
```