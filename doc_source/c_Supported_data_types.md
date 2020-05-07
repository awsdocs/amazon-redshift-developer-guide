# Data types<a name="c_Supported_data_types"></a>

**Topics**
+ [Multibyte characters](#c_Supported_data_types-multi-byte-characters)
+ [Numeric types](r_Numeric_types201.md)
+ [Character types](r_Character_types.md)
+ [Datetime types](r_Datetime_types.md)
+ [Boolean type](r_Boolean_type.md)
+ [Type compatibility and conversion](r_Type_conversion.md)

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