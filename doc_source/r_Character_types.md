# Character types<a name="r_Character_types"></a>

**Topics**
+ [Storage and ranges](#r_Character_types-storage-and-ranges)
+ [CHAR or CHARACTER](#r_Character_types-char-or-character)
+ [VARCHAR or CHARACTER VARYING](#r_Character_types-varchar-or-character-varying)
+ [NCHAR and NVARCHAR types](#r_Character_types-nchar-and-nvarchar-types)
+ [TEXT and BPCHAR types](#r_Character_types-text-and-bpchar-types)
+ [Significance of trailing blanks](#r_Character_types-significance-of-trailing-blanks)
+ [Examples with character types](r_Examples_with_character_types.md)

Character data types include CHAR \(character\) and VARCHAR \(character varying\)\. 

## Storage and ranges<a name="r_Character_types-storage-and-ranges"></a>

CHAR and VARCHAR data types are defined in terms of bytes, not characters\. A CHAR column can only contain single\-byte characters, so a CHAR\(10\) column can contain a string with a maximum length of 10 bytes\. A VARCHAR can contain multibyte characters, up to a maximum of four bytes per character\. For example, a VARCHAR\(12\) column can contain 12 single\-byte characters, 6 two\-byte characters, 4 three\-byte characters, or 3 four\-byte characters\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Character_types.html)

**Note**  
The CREATE TABLE syntax supports the MAX keyword for character data types\. For example:  

```
create table test(col1 varchar(max));
```
The MAX setting defines the width of the column as 4096 bytes for CHAR or 65535 bytes for VARCHAR\.

## CHAR or CHARACTER<a name="r_Character_types-char-or-character"></a>

Use a CHAR or CHARACTER column to store fixed\-length strings\. These strings are padded with blanks, so a CHAR\(10\) column always occupies 10 bytes of storage\. 

```
char(10)
```

 A CHAR column without a length specification results in a CHAR\(1\) column\. 

## VARCHAR or CHARACTER VARYING<a name="r_Character_types-varchar-or-character-varying"></a>

Use a VARCHAR or CHARACTER VARYING column to store variable\-length strings with a fixed limit\. These strings are not padded with blanks, so a VARCHAR\(120\) column consists of a maximum of 120 single\-byte characters, 60 two\-byte characters, 40 three\-byte characters, or 30 four\-byte characters\.

```
varchar(120)
```

If you use the VARCHAR data type without a length specifier in a CREATE TABLE statement, the default length is 256\. If used in an expression, the size of the output is determined using the input expression \(up to 65535\)\.

## NCHAR and NVARCHAR types<a name="r_Character_types-nchar-and-nvarchar-types"></a>

You can create columns with the NCHAR and NVARCHAR types \(also known as NATIONAL CHARACTER and NATIONAL CHARACTER VARYING types\)\. These types are converted to CHAR and VARCHAR types, respectively, and are stored in the specified number of bytes\. 

An NCHAR column without a length specification is converted to a CHAR\(1\) column\. 

An NVARCHAR column without a length specification is converted to a VARCHAR\(256\) column\. 

## TEXT and BPCHAR types<a name="r_Character_types-text-and-bpchar-types"></a>

You can create an Amazon Redshift table with a TEXT column, but it is converted to a VARCHAR\(256\) column that accepts variable\-length values with a maximum of 256 characters\. 

You can create an Amazon Redshift column with a BPCHAR \(blank\-padded character\) type, which Amazon Redshift converts to a fixed\-length CHAR\(256\) column\. 

## Significance of trailing blanks<a name="r_Character_types-significance-of-trailing-blanks"></a>

Both CHAR and VARCHAR data types store strings up to *n* bytes in length\. An attempt to store a longer string into a column of these types results in an error, unless the extra characters are all spaces \(blanks\), in which case the string is truncated to the maximum length\. If the string is shorter than the maximum length, CHAR values are padded with blanks, but VARCHAR values store the string without blanks\.

Trailing blanks in CHAR values are always semantically insignificant\. They are disregarded when you compare two CHAR values, not included in LENGTH calculations, and removed when you convert a CHAR value to another string type\. 

Trailing spaces in VARCHAR and CHAR values are treated as semantically insignificant when values are compared\.

Length calculations return the length of VARCHAR character strings with trailing spaces included in the length\. Trailing blanks are not counted in the length for fixed\-length character strings\.