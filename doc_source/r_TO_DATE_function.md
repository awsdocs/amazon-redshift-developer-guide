# TO\_DATE<a name="r_TO_DATE_function"></a>

TO\_DATE converts a date represented by a character string to a DATE data type\. 

## Syntax<a name="r_TO_DATE_function-synopsis"></a>

```
TO_DATE(string, format)
```

```
TO_DATE(string, format, is_strict)
```

## Arguments<a name="r_TO_DATE_function-arguments"></a>

 *string*   
A string to be converted\. 

 *format*   
A string literal that defines the format of the input *string*, in terms of its date parts\. For a list of valid day, month, and year formats, see [Datetime format strings](r_FORMAT_strings.md)\. 

 *is\_strict*   
An optional Boolean value that specifies whether an error is returned if an input date value is out of range\. When *is\_strict* is set to `TRUE`, an error is returned if there is an out of range value\. When *is\_strict* is set to `FALSE`, which is the default, then overflow values are accepted\. 

## Return type<a name="r_TO_DATE_function-return-type"></a>

TO\_DATE returns a DATE, depending on the *format* value\. 

If the conversion to *format* fails, then an error is returned\. 

## Examples<a name="r_TO_DATE_function-example"></a>

 The following SQL statement converts the date `02 Oct 2001` into a date data type\.

```
select to_date('02 Oct 2001', 'DD Mon YYYY');

to_date
------------
2001-10-02
(1 row)
```

 The following SQL statement converts the string `20010631` to a date\.

```
select to_date('20010631', 'YYYYMMDD', FALSE);
```

The result is July 1, 2001, because there are only 30 days in June\.

```
to_date
------------
2001-07-01
```

 The following SQL statement converts the string `20010631` to a date: 

```
select to_date('20010631', 'YYYYMMDD', TRUE);
```

The result is an error because there are only 30 days in June\.

```
ERROR:  date/time field date value out of range: 2001-6-31
```