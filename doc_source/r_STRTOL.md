# STRTOL function<a name="r_STRTOL"></a>

Converts a string expression of a number of the specified base to the equivalent integer value\. The converted value must be within the signed 64\-bit range\. 

## Syntax<a name="r_STRTOL-syntax"></a>

```
STRTOL(num_string, base)
```

## Arguments<a name="r_STRTOL-arguments"></a>

 *num\_string*   
String expression of a number to be converted\. If *num\_string* is empty \( `''` \) or begins with the null character \(`'\0'`\), the converted value is 0\. If *num\_string* is a column containing a NULL value, STRTOL returns NULL\. The string can begin with any amount of white space, optionally followed by a single plus '`+`' or minus '`-`' sign to indicate positive or negative\. The default is '`+`'\. If *base* is `16`, the string can optionally begin with '`0x`'\. 

*base*  
Integer between 2 and 36\.

## Return type<a name="r_STRTOL-return-type"></a>

BIGINT\. If *num\_string* is null, returns NULL\.

## Examples<a name="r_STRTOL-examples"></a>

The following examples convert string and base value pairs to integers:

```
select strtol('0xf',16);

 strtol
--------
     15
(1 row)

select strtol('abcd1234',16);
   strtol
------------
 2882343476
(1 row)

select strtol('1234567', 10);
 strtol
---------
 1234567
(1 row)

select strtol('1234567', 8);
 strtol
--------
 342391
(1 row)

select strtol('110101', 2);
 strtol
--------
     53

select strtol('\0', 2);
 strtol
--------
     0
```