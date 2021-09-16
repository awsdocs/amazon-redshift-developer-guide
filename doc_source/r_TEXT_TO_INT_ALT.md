# TEXT\_TO\_INT\_ALT<a name="r_TEXT_TO_INT_ALT"></a>

TEXT\_TO\_INT\_ALT converts a character string to an integer using Teradata\-style formatting\. Fraction digits in the result are truncated\.

## Syntax<a name="r_TEXT_TO_INT_ALT-synopsis"></a>

```
TEXT_TO_INT_ALT (expression [ , 'format'])
```

## Arguments<a name="r_TEXT_TO_INT_ALT-arguments"></a>

 *expression*   
An expression that results in one or more CHAR or VARCHAR values, such as a column name or literal string\. Converting null values returns nulls\. The function converts blank or empty strings to 0\. 

 *format*   
A string literal that defines the format of the input expression\. For more information about the formatting characters you can specify, see [Teradata\-style formatting characters for numeric data](r_Numeric-format-teradata.md)\. 

## Return type<a name="r_TEXT_TO_INT_ALT-return-type"></a>

TEXT\_TO\_INT\_ALT returns an INTEGER value\.

The fractional portion of the cast result is truncated\.

Amazon Redshift returns an error if the conversion to the *format* phrase that you specify isn't successful\.

## Examples<a name="r_TEXT_TO_INT_ALT-examples"></a>

The following example converts the input *expression* string '123\-' to the integer \-123\.

```
select text_to_int_alt('123-');
```

```
text_to_int_alt
----------
      -123
```

The following example converts the input *expression* string '2147483647\+' to the integer 2147483647\.

```
select text_to_int_alt('2147483647+');
```

```
text_to_int_alt
----------
2147483647
```

The following example converts the exponential input *expression* string '\-123E\-2' to the integer \-1\.

```
select text_to_int_alt('-123E-2');
```

```
text_to_int_alt
----------
        -1
```

The following example converts the input *expression* string '2147483647\+' to the integer 2147483647\.

```
select text_to_int_alt('2147483647+');
```

```
text_to_int_alt
----------
2147483647
```

The following example converts the input *expression* string '123\{' with the *format* phrase '999S' to the integer 1230\. The S character indicates a Signed Zoned Decimal\. For more information, see [Teradata\-style formatting characters for numeric data](r_Numeric-format-teradata.md)\.

```
text_to_int_alt('123{', '999S');
```

```
text_to_int_alt
----------
      1230
```

The following example converts the input *expression* string 'USD123' with the *format* phrase 'C9\(I\)' to the integer 123\. See [Teradata\-style formatting characters for numeric data](r_Numeric-format-teradata.md)\.

```
text_to_int_alt('USD123', 'C9(I)');
```

```
text_to_int_alt
----------
       123
```

The following example specifies a table column as the input *expression*\.

```
select text_to_int_alt(a), text_to_int_alt(b) from t_text2int order by 1;
```

```
 text_to_int_alt | text_to_int_alt
-----------------+-----------------
            -123 |            -123
            -123 |            -123
             123 |             123
             123 |             123
```

Following is the table definition and the insert statement for this example\.

```
create table t_text2int (a varchar(200), b char(200));
```

```
insert into t_text2int VALUES('123', '123'),('123.123', '123.123'), ('-123', '-123'), ('123-', '123-');
```