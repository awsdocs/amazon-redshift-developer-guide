# TO\_DATE<a name="r_TO_DATE_function"></a>

TO\_DATE converts a date represented in a character string to a DATE data type\. 

 The second argument is a format string that indicates how the character string should be parsed to create the date value\. 

## Syntax<a name="r_TO_DATE_function-synopsis"></a>

```
TO_DATE (string, format)
```

## Arguments<a name="r_TO_DATE_function-arguments"></a>

 *string*   
 String to be converted\. 

 *format*   
A string literal that defines the format of the input *string*, in terms of its date parts\. For a list of valid formats, see [Datetime format strings](r_FORMAT_strings.md)\. 

## Return type<a name="r_TO_DATE_function-return-type"></a>

TO\_DATE returns a DATE, depending on the *format* value\. 

If the conversion to *format* fails, then an error is returned\. 

## Example<a name="r_TO_DATE_function-example"></a>

 The following command converts the date `02 Oct 2001` into the default date format: 

```
select to_date ('02 Oct 2001', 'DD Mon YYYY');

to_date
------------
2001-10-02
(1 row)
```