# REVERSE function<a name="r_REVERSE"></a>

The REVERSE function operates on a string and returns the characters in reverse order\. For example, `reverse('abcde')` returns `edcba`\. This function works on numeric and date data types as well as character data types; however, in most cases it has practical value for character strings\. 

## Syntax<a name="r_REVERSE-synopsis"></a>

```
REVERSE ( expression )
```

## Argument<a name="r_REVERSE-argument"></a>

 *expression*   
An expression with a character, date, timestamp, or numeric data type that represents the target of the character reversal\. All expressions are implicitly converted to variable\-length character strings\. Trailing blanks in fixed\-width character strings are ignored\. 

## Return type<a name="r_REVERSE-return-type"></a>

REVERSE returns a VARCHAR\. 

## Examples<a name="r_REVERSE-examples"></a>

Select five distinct city names and their corresponding reversed names from the USERS table: 

```
select distinct city as cityname, reverse(cityname)
from users order by city limit 5;

cityname | reverse
---------+----------
Aberdeen | needrebA
Abilene  | enelibA
Ada      | adA
Agat     | tagA
Agawam   | mawagA
(5 rows)
```

Select five sales IDs and their corresponding reversed IDs cast as character strings: 

```
select salesid, reverse(salesid)::varchar
from sales order by salesid desc limit 5;

salesid | reverse
--------+---------
 172456 | 654271
 172455 | 554271
 172454 | 454271
 172453 | 354271
 172452 | 254271
(5 rows)
```