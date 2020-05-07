# Type compatibility and conversion<a name="r_Type_conversion"></a>

Following, you can find a discussion about how type conversion rules and data type compatibility work in Amazon Redshift\.

## Compatibility<a name="r_Type_conversion-compatibility"></a>

 Data type matching and matching of literal values and constants to data types occurs during various database operations, including the following: 
+ Data manipulation language \(DML\) operations on tables 
+ UNION, INTERSECT, and EXCEPT queries 
+ CASE expressions 
+ Evaluation of predicates, such as LIKE and IN 
+ Evaluation of SQL functions that do comparisons or extractions of data 
+ Comparisons with mathematical operators 

The results of these operations depend on type conversion rules and data type compatibility\. *Compatibility* implies that a one\-to\-one matching of a certain value and a certain data type is not always required\. Because some data types are *compatible*, an implicit conversion, or *coercion*, is possible \(for more information, see [Implicit conversion types](#implicit-conversion-types)\)\. When data types are incompatible, you can sometimes convert a value from one data type to another by using an explicit conversion function\. 

## General compatibility and conversion rules<a name="r_Type_conversion-general-compatibility-and-conversion-rules"></a>

Note the following compatibility and conversion rules: 
+ In general, data types that fall into the same type category \(such as different numeric data types\) are compatible and can be implicitly converted\. 

  For example, with implicit conversion you can insert a decimal value into an integer column\. The decimal is rounded to produce a whole number\. Or you can extract a numeric value, such as `2008`, from a date and insert that value into an integer column\. 
+ Numeric data types enforce overflow conditions that occur when you attempt to insert out\-of\-range values\. For example, a decimal value with a precision of 5 does not fit into a decimal column that was defined with a precision of 4\. An integer or the whole part of a decimal is never truncated; however, the fractional part of a decimal can be rounded up or down, as appropriate\. However, results of explicit casts of values selected from tables are not rounded\.
+ Different types of character strings are compatible; VARCHAR column strings containing single\-byte data and CHAR column strings are comparable and implicitly convertible\. VARCHAR strings that contain multibyte data are not comparable\. Also, you can convert a character string to a date, timestamp, or numeric value if the string is an appropriate literal value; any leading or trailing spaces are ignored\. Conversely, you can convert a date, timestamp, or numeric value to a fixed\-length or variable\-length character string\.
**Note**  
A character string that you want to cast to a numeric type must contain a character representation of a number\. For example, you can cast the strings `'1.0'` or `'5.9'` to decimal values, but you cannot cast the string `'ABC'` to any numeric type\.
+ If you compare numeric values with character strings, the numeric values are converted to character strings\. To enforce the opposite conversion \(converting character strings to numeric values\), use an explicit function, such as [CAST and CONVERT](r_CAST_function.md)\. 
+ To convert 64\-bit DECIMAL or NUMERIC values to a higher precision, you must use an explicit conversion function such as the CAST or CONVERT functions\. 
+ When converting DATE or TIMESTAMP to TIMESTAMPTZ, DATE or TIMESTAMP are assumed to use the current session time zone\. The session time zone is UTC by default\. For more information about setting the session time zone, see [timezone](r_timezone_config.md)\. 
+ Similarly, TIMESTAMPTZ is converted to DATE or TIMESTAMP based on the current session time zone\. The session time zone is UTC by default\. After the conversion, time zone information is dropped\.
+ Character strings that represent a time stamp with time zone specified are converted to TIMESTAMPTZ using the specified time zone\. If the time zone is omitted, the current session time zone is used, which is UTC by default\.

## Implicit conversion types<a name="implicit-conversion-types"></a>

There are two types of implicit conversions: 
+ Implicit conversions in assignments, such as setting values in INSERT or UPDATE commands\.
+ Implicit conversions in expressions, such as performing comparisons in the WHERE clause\.

The table following lists the data types that can be converted implicitly in assignments or expressions\. You can also use an explicit conversion function to perform these conversions\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Type_conversion.html)

**Note**  
Implicit conversions between TIMESTAMPTZ, TIMESTAMP, DATE, or character strings use the current session time zone\. For information about setting the current time zone, see [timezone](r_timezone_config.md)\.  
The GEOMETRY data type can't be implicitly converted to any other data type\. For more information, see [CAST and CONVERT functions](r_CAST_function.md)\. 