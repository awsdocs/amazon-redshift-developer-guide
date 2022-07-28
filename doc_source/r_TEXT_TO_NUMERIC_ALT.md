# TEXT\_TO\_NUMERIC\_ALT<a name="r_TEXT_TO_NUMERIC_ALT"></a>

TEXT\_TO\_NUMERIC\_ALT performs a Teradata\-style cast operation to convert a character string to a numeric data format\. 

## Syntax<a name="r_TEXT_TO_NUMERIC_ALT-synopsis"></a>

```
TEXT_TO_NUMERIC_ALT (expression [, 'format'] [, precision, scale])
```

## Arguments<a name="r_TEXT_TO_NUMERIC_ALT-arguments"></a>

 *expression*   
An expression that evaluates to one or more CHAR or VARCHAR values, such as a column name or a literal\. Converting null values returns nulls\. Blank or empty strings are converted to 0\. 

 *format*   
A string literal that defines the format of the input expression\. For more information, see [Teradata\-style formatting characters for numeric data](r_Numeric-format-teradata.md)\. 

 *precision*   
The number of digits in the numeric result\. The default is 38\. 

 *scale*   
The number of digits to the right of the decimal point in the numeric result\. The default is 0\. 

## Return type<a name="r_TEXT_TO_NUMERIC_ALT-return-type"></a>

TEXT\_TO\_NUMERIC\_ALT returns a DECIMAL number\.

Amazon Redshift returns an error if the conversion to the *format* phrase that you specify isn't successful\.

Amazon Redshift casts the input *expression* string to the numeric type with the highest precision that you specify for that type in the *precision* option\. If the length of the numeric value exceeds the value that you specify for *precision*, Amazon Redshift rounds the numeric value according to the following rules:
+ If the length of the cast result exceeds the length that you specify in the *format* phrase, Amazon Redshift returns an error\.
+ If the result is cast to a numeric value, the result is rounded to the closest value\. If the fractional portion is exactly midway between the upper and lower cast result, the result is rounded to the nearest even value\.

## Examples<a name="r_TEXT_TO_NUMERIC_ALT-examples"></a>

The following example converts the input *expression* string '1\.5' to the numeric value '2'\. Because the statement doesn't specify *scale*, the *scale* defaults to 0 and the cast result doesn't include a fraction result\. Because \.5 is midway between 1 and 2, the cast result is rounded to the even value of 2\.

```
select text_to_numeric_alt('1.5');
```

```
 text_to_numeric_alt
---------------------
                   2
```

The following example converts the input *expression* string '2\.51' to the numeric value 3\. Because the statement doesn't specify a *scale* value, the *scale* defaults to 0 and the cast result doesn't include a fraction result\. Because \.51 is closer to 3 than 2, the cast result is rounded to the value of 3\.

```
select text_to_numeric_alt('2.51');
```

```
 text_to_numeric_alt
---------------------
                   3
```

The following example converts the input *expression* string 123\.52501 with a *precision* of 10 and a *scale* of 2 to the numeric value 123\.53\. 

```
select text_to_numeric_alt('123.52501', 10, 2);
```

```
 text_to_numeric_alt
---------------------
               123.53
```

The following example specifies a table column as the input *expression*\.

```
select text_to_numeric_alt(a), text_to_numeric_alt(b) from t_text2numeric order by 1;
```

```
           text_to_numeric_alt           |           text_to_numeric_alt
-----------------------------------------+-----------------------------------------
 -99999999999999999999999999999999999999 | -99999999999999999999999999999999999999
                                  -12300 |                                  -12300
                                     123 |                                     123
                                     123 |                                     123
  99999999999999999999999999999999999999 |  99999999999999999999999999999999999999
```

Following is the table definition and the insert statement for this example\.

```
create table  t_text2numeric (a varchar(200), b char(200));
```

```
insert into  t_text2numeric values
('123', '123'),
('+123.456', '+123.456'),
('-' || repeat('9', 38), '-' || repeat('9', 38)),
(repeat('9', 38) || '+', repeat('9', 38) || '+'),
('-123E2', '-123E2');
```