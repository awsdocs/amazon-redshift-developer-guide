# Names and identifiers<a name="r_names"></a>

Names identify database objects, including tables and columns, as well as users and passwords\. The terms *name* and *identifier* can be used interchangeably\. There are two types of identifiers, standard identifiers and quoted or delimited identifiers\. Identifiers must consist of only UTF\-8 printable characters\. ASCII letters in standard and delimited identifiers are case\-insensitive and are folded to lowercase in the database\. In query results, column names are returned as lowercase by default\. To return column names in uppercase, set the [describe\_field\_name\_in\_uppercase](r_describe_field_name_in_uppercase.md) configuration parameter to **true**\.

## Standard identifiers<a name="r_names-standard-identifiers"></a>

Standard SQL identifiers adhere to a set of rules and must: 
+ Begin with an ASCII single\-byte alphabetic character or underscore character, or a UTF\-8 multibyte character two to four bytes long\.
+ Subsequent characters can be ASCII single\-byte alphanumeric characters, underscores, or dollar signs, or UTF\-8 multibyte characters two to four bytes long\.
+ Be between 1 and 127 bytes in length, not including quotation marks for delimited identifiers\. 
+ Contain no quotation marks and no spaces\. 
+ Not be a reserved SQL key word\.

## Delimited identifiers<a name="r_names-delimited-identifiers"></a>

Delimited identifiers \(also known as quoted identifiers\) begin and end with double quotation marks \("\)\. If you use a delimited identifier, you must use the double quotation marks for every reference to that object\. The identifier can contain any standard UTF\-8 printable characters other than the double quotation mark itself\. Therefore, you can create column or table names that include otherwise illegal characters, such as spaces or the percent symbol\.

ASCII letters in delimited identifiers are case\-insensitive and are folded to lowercase\. To use a double quotation mark in a string, you must precede it with another double quotation mark character\. 

## Examples<a name="r_names-examples"></a>

This table shows examples of delimited identifiers, the resulting output, and a discussion: 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_names.html)

To create a table named group with a column named this "is it": 

```
create table "group" (
"This ""IS IT""" char(10));
```

The following queries return the same result: 

```
select "This ""IS IT"""
from "group";

this "is it"
--------------
(0 rows)
```

```
select "this ""is it"""
from "group";

this "is it"
--------------
(0 rows)
```

The following fully qualified `table.column` syntax also returns the same result: 

```
select "group"."this ""is it"""
from "group";

this "is it"
--------------
(0 rows)
```

The following CREATE TABLE command creates a table with a slash in a column name: 

```
create table if not exists city_slash_id(
                  "city/id" integer not null,
                  state char(2) not null);
```