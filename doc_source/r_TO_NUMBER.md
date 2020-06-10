# TO\_NUMBER<a name="r_TO_NUMBER"></a>

TO\_NUMBER converts a string to a numeric \(decimal\) value\. 

## Syntax<a name="r_TO_NUMBER-synopsis"></a>

```
to_number(string, format)
```

## Arguments<a name="r_TO_NUMBER-arguments"></a>

 *string*   
String to be converted\. The format must be a literal value\. 

 *format*   
The second argument is a format string that indicates how the character string should be parsed to create the numeric value\. For example, the format `'99D999'` specifies that the string to be converted consists of five digits with the decimal point in the third position\. For example, `to_number('12.345','99D999')` returns `12.345` as a numeric value\. For a list of valid formats, see [ Numeric Format Strings](r_Numeric_formating.md)\. 

## Return type<a name="r_TO_NUMBER-return-type"></a>

TO\_NUMBER returns a DECIMAL number\. 

If the conversion to *format* fails, then an error is returned\. 

## Examples<a name="r_TO_NUMBER-examples"></a>

The following example converts the string `12,454.8-` to a number: 

```
select to_number('12,454.8-', '99G999D9S');

to_number
-----------
-12454.8
(1 row)
```