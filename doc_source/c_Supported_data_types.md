# Data types<a name="c_Supported_data_types"></a>

**Topics**
+ [Multibyte characters](#c_Supported_data_types-multi-byte-characters)
+ [Numeric types](r_Numeric_types201.md)
+ [Character types](r_Character_types.md)
+ [Datetime types](r_Datetime_types.md)
+ [Boolean type](r_Boolean_type.md)
+ [HLLSKETCH type](r_HLLSKTECH_type.md)
+ [Type compatibility and conversion](#r_Type_conversion)

Each value that Amazon Redshift stores or retrieves has a data type with a fixed set of associated properties\. Data types are declared when tables are created\. A data type constrains the set of values that a column or argument can contain\. 

The following table lists the data types that you can use in Amazon Redshift tables\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_Supported_data_types.html)

**Note**  
For information about unsupported data types, such as "char" \(notice that char is enclosed in quotation marks\), see [Unsupported PostgreSQL data types](c_unsupported-postgresql-datatypes.md)\.

## Multibyte characters<a name="c_Supported_data_types-multi-byte-characters"></a>

The VARCHAR data type supports UTF\-8 multibyte characters up to a maximum of four bytes\. Five\-byte or longer characters are not supported\. To calculate the size of a VARCHAR column that contains multibyte characters, multiply the number of characters by the number of bytes per character\. For example, if a string has four Chinese characters, and each character is three bytes long, then you will need a VARCHAR\(12\) column to store the string\.

VARCHAR does not support the following invalid UTF\-8 codepoints:
+ 0xD800 \- 0xDFFF

  \(Byte sequences: ED A0 80 \- ED BF BF\)
+ 0xFDD0 \- 0xFDEF, 0xFFFE, and 0xFFFF

   \(Byte sequences: EF B7 90 \- EF B7 AF, EF BF BE, and EF BF BF\)

The CHAR data type does not support multibyte characters\.

## Type compatibility and conversion<a name="r_Type_conversion"></a>

Following, you can find a discussion about how type conversion rules and data type compatibility work in Amazon Redshift\.

### Compatibility<a name="r_Type_conversion-compatibility"></a>

 Data type matching and matching of literal values and constants to data types occurs during various database operations, including the following: 
+ Data manipulation language \(DML\) operations on tables 
+ UNION, INTERSECT, and EXCEPT queries 
+ CASE expressions 
+ Evaluation of predicates, such as LIKE and IN 
+ Evaluation of SQL functions that do comparisons or extractions of data 
+ Comparisons with mathematical operators 

The results of these operations depend on type conversion rules and data type compatibility\. *Compatibility* implies that a one\-to\-one matching of a certain value and a certain data type is not always required\. Because some data types are *compatible*, an implicit conversion, or *coercion*, is possible \(for more information, see [Implicit conversion types](#implicit-conversion-types)\)\. When data types are incompatible, you can sometimes convert a value from one data type to another by using an explicit conversion function\. 

### General compatibility and conversion rules<a name="r_Type_conversion-general-compatibility-and-conversion-rules"></a>

Note the following compatibility and conversion rules: 
+ In general, data types that fall into the same type category \(such as different numeric data types\) are compatible and can be implicitly converted\. 

  For example, with implicit conversion you can insert a decimal value into an integer column\. The decimal is rounded to produce a whole number\. Or you can extract a numeric value, such as `2008`, from a date and insert that value into an integer column\. 
+ Numeric data types enforce overflow conditions that occur when you attempt to insert out\-of\-range values\. For example, a decimal value with a precision of 5 does not fit into a decimal column that was defined with a precision of 4\. An integer or the whole part of a decimal is never truncated; however, the fractional part of a decimal can be rounded up or down, as appropriate\. However, results of explicit casts of values selected from tables are not rounded\.
+ Different types of character strings are compatible; VARCHAR column strings containing single\-byte data and CHAR column strings are comparable and implicitly convertible\. VARCHAR strings that contain multibyte data are not comparable\. Also, you can convert a character string to a date, time, timestamp, or numeric value if the string is an appropriate literal value; any leading or trailing spaces are ignored\. Conversely, you can convert a date, time, timestamp, or numeric value to a fixed\-length or variable\-length character string\.
**Note**  
A character string that you want to cast to a numeric type must contain a character representation of a number\. For example, you can cast the strings `'1.0'` or `'5.9'` to decimal values, but you cannot cast the string `'ABC'` to any numeric type\.
+ If you compare numeric values with character strings, the numeric values are converted to character strings\. To enforce the opposite conversion \(converting character strings to numeric values\), use an explicit function, such as [CAST and CONVERT](r_CAST_function.md)\. 
+ To convert 64\-bit DECIMAL or NUMERIC values to a higher precision, you must use an explicit conversion function such as the CAST or CONVERT functions\. 
+ When converting DATE or TIMESTAMP to TIMESTAMPTZ, or converting TIME to TIMETZ, the time zone is set to the current session time zone\. The session time zone is UTC by default\. For more information about setting the session time zone, see [timezone](r_timezone_config.md)\. 
+ Similarly, TIMESTAMPTZ is converted to DATE, TIME, or TIMESTAMP based on the current session time zone\. The session time zone is UTC by default\. After the conversion, time zone information is dropped\.
+ Character strings that represent a timestamp with time zone specified are converted to TIMESTAMPTZ using the current session time zone, which is UTC by default\. Likewise, character strings that represent a time with time zone specified are converted to TIMETZ using the current session time zone, which is UTC by default\.

### Implicit conversion types<a name="implicit-conversion-types"></a>

There are two types of implicit conversions: 
+ Implicit conversions in assignments, such as setting values in INSERT or UPDATE commands\.
+ Implicit conversions in expressions, such as performing comparisons in the WHERE clause\.

The table following lists the data types that can be converted implicitly in assignments or expressions\. You can also use an explicit conversion function to perform these conversions\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_Supported_data_types.html)

**Note**  
Implicit conversions between TIMESTAMPTZ, TIMESTAMP, DATE, TIME, TIMETZ, or character strings use the current session time zone\. For information about setting the current time zone, see [timezone](r_timezone_config.md)\.  
The GEOMETRY data type can't be implicitly converted to any other data type\. For more information, see [CAST and CONVERT functions](r_CAST_function.md)\. 