# TRIM function<a name="r_TRIM"></a>

The TRIM function trims a string by removing leading and trailing blanks or by removing characters that match an optional specified string\. 

## Syntax<a name="r_TRIM-synopsis"></a>

```
TRIM( [ BOTH ] ['characters' FROM ] string ] )
```

## Arguments<a name="r_TRIM-arguments"></a>

 *characters*   
\(Optional\) The characters to be trimmed from the string\. If this parameter is omitted, blanks are trimmed\.

 *string*   
The string to be trimmed\. 

## Return type<a name="r_TRIM-return-type"></a>

The TRIM function returns a VARCHAR or CHAR string\. If you use the TRIM function with a SQL command, Amazon Redshift implicitly converts the results to VARCHAR\. If you use the TRIM function in the SELECT list for a SQL function, Amazon Redshift does not implicitly convert the results, and you might need to perform an explicit conversion to avoid a data type mismatch error\. See the [CAST and CONVERT functions](r_CAST_function.md) and [CONVERT](r_CAST_function.md#convert-function) functions for information about explicit conversions\.

## Example<a name="r_TRIM-example"></a>

The following example removes the double quotation marks that surround the string `"dog"`: 

```
select trim('"' FROM '"dog"');

btrim
-------
dog
(1 row)
```