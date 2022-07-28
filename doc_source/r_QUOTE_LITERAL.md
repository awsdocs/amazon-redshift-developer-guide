# QUOTE\_LITERAL function<a name="r_QUOTE_LITERAL"></a>

The QUOTE\_LITERAL function returns the specified string as a quoted string so that it can be used as a string literal in a SQL statement\. If the input parameter is a number, QUOTE\_LITERAL treats it as a string\. Appropriately doubles any embedded single quotation marks and backslashes\. 

## Syntax<a name="r_QUOTE_LITERAL-synopsis"></a>

```
QUOTE_LITERAL(string)
```

## Argument<a name="r_QUOTE_LITERAL-argument"></a>

 *string*   
The input parameter is a CHAR or VARCHAR string\. 

## Return type<a name="r_QUOTE_LITERAL-return-type"></a>

The QUOTE\_LITERAL function returns a string that is the same data type as the input string \(CHAR or VARCHAR\)\. 

## Example<a name="r_QUOTE_LITERAL-example"></a>

The following example returns the CATID column surrounded by quotation marks\. The ordering now treats this column as a string\. 

```
select quote_literal(catid), catname
from category
order by 1,2;

quote_literal |  catname
--------------+-----------
'1'           | MLB
'10'          | Jazz
'11'          | Classical
'2'           | NHL
'3'           | NFL
'4'           | NBA
'5'           | MLS
'6'           | Musicals
'7'           | Plays
'8'           | Opera
'9'           | Pop
(11 rows)
```