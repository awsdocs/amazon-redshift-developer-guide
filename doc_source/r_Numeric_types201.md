# Numeric types<a name="r_Numeric_types201"></a>

**Topics**
+ [Integer types](#r_Numeric_types201-integer-types)
+ [DECIMAL or NUMERIC type](#r_Numeric_types201-decimal-or-numeric-type)
+ [Notes about using 128\-bit DECIMAL or NUMERIC columns](#r_Numeric_types201-notes-about-using-128-bit-decimal-or-numeric-columns)
+ [Floating\-Point types](#r_Numeric_types201-floating-point-types)
+ [Computations with numeric values](r_numeric_computations201.md)
+ [Integer and floating\-point literals](r_numeric_literals201.md)
+ [Examples with numeric types](r_Examples_with_numeric_types201.md)

Numeric data types include integers, decimals, and floating\-point numbers\. 

## Integer types<a name="r_Numeric_types201-integer-types"></a>

Use the SMALLINT, INTEGER, and BIGINT data types to store whole numbers of various ranges\. You cannot store values outside of the allowed range for each type\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Numeric_types201.html)

## DECIMAL or NUMERIC type<a name="r_Numeric_types201-decimal-or-numeric-type"></a>

Use the DECIMAL or NUMERIC data type to store values with a *user\-defined precision*\. The DECIMAL and NUMERIC keywords are interchangeable\. In this document, *decimal* is the preferred term for this data type\. The term *numeric* is used generically to refer to integer, decimal, and floating\-point data types\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Numeric_types201.html)

Define a DECIMAL column in a table by specifying a *precision* and *scale*: 

```
decimal(precision, scale)
```

 *precision*   
The total number of significant digits in the whole value: the number of digits on both sides of the decimal point\. For example, the number `48.2891` has a precision of 6 and a scale of 4\. The default precision, if not specified, is 18\. The maximum precision is 38\.  
 If the number of digits to the left of the decimal point in an input value exceeds the precision of the column minus its scale, the value cannot be copied into the column \(or inserted or updated\)\. This rule applies to any value that falls outside the range of the column definition\. For example, the allowed range of values for a `numeric(5,2)` column is `-999.99` to `999.99`\. 

 *scale*   
The number of decimal digits in the fractional part of the value, to the right of the decimal point\. Integers have a scale of zero\. In a column specification, the scale value must be less than or equal to the precision value\. The default scale, if not specified, is 0\. The maximum scale is 37\.  
If the scale of an input value that is loaded into a table is greater than the scale of the column, the value is rounded to the specified scale\. For example, the PRICEPAID column in the SALES table is a DECIMAL\(8,2\) column\. If a DECIMAL\(8,4\) value is inserted into the PRICEPAID column, the value is rounded to a scale of 2\.   

```
insert into sales
values (0, 8, 1, 1, 2000, 14, 5, 4323.8951, 11.00, null);

select pricepaid, salesid from sales where salesid=0;

pricepaid | salesid
-----------+---------
4323.90 |       0
(1 row)
```
 However, results of explicit casts of values selected from tables are not rounded\.

**Note**  
The maximum positive value that you can insert into a DECIMAL\(19,0\) column is `9223372036854775807` \(263 \-1\)\. The maximum negative value is `-9223372036854775807`\. For example, an attempt to insert the value `9999999999999999999` \(19 nines\) will cause an overflow error\. Regardless of the placement of the decimal point, the largest string that Amazon Redshift can represent as a DECIMAL number is `9223372036854775807`\. For example, the largest value that you can load into a DECIMAL\(19,18\) column is `9.223372036854775807`\. These rules derive from the internal storage of DECIMAL values as 8\-byte integers\. Amazon Redshift recommends that you do not define DECIMAL values with 19 digits of precision unless that precision is necessary\.

## Notes about using 128\-bit DECIMAL or NUMERIC columns<a name="r_Numeric_types201-notes-about-using-128-bit-decimal-or-numeric-columns"></a>

Do not arbitrarily assign maximum precision to DECIMAL columns unless you are certain that your application requires that precision\. 128\-bit values use twice as much disk space as 64\-bit values and can slow down query execution time\. 

## Floating\-Point types<a name="r_Numeric_types201-floating-point-types"></a>

Use the REAL and DOUBLE PRECISION data types to store numeric values with *variable precision*\. These types are *inexact* types, meaning that some values are stored as approximations, such that storing and returning a specific value may result in slight discrepancies\. If you require exact storage and calculations \(such as for monetary amounts\), use the DECIMAL data type\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Numeric_types201.html)

For example, note the results of the following inserts into a REAL column: 

```
create table real1(realcol real);

insert into real1 values(12345.12345);

insert into real1 values(123456.12345);

select * from real1;
realcol
---------
123456
12345.1
(2 rows)
```

These inserted values are truncated to meet the limitation of 6 significant digits of precision for REAL columns\. 