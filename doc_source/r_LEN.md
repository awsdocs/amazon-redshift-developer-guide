# LEN function<a name="r_LEN"></a>

Returns the length of the specified string as the number of characters\. 

## Syntax<a name="r_LEN-synopsis"></a>

LEN is a synonym of [LENGTH function](r_LENGTH.md), [CHAR\_LENGTH function](r_CHAR_LENGTH.md), [CHARACTER\_LENGTH function](r_CHARACTER_LENGTH.md), and [TEXTLEN function](r_TEXTLEN.md)\. 

```
LEN(expression)
```

## Argument<a name="r_LEN-argument"></a>

 *expression*   
The input parameter is a CHAR or VARCHAR text string\. 

## Return type<a name="r_LEN-return-type"></a>

The LEN function returns an integer indicating the number of characters in the input string\. The LEN function returns the actual number of characters in multi\-byte strings, not the number of bytes\. For example, a VARCHAR\(12\) column is required to store three four\-byte Chinese characters\. The LEN function will return 3 for that same string\. To get the length of a string in bytes, use the [OCTET\_LENGTH](r_OCTET_LENGTH.md) function\.

## Usage notes<a name="r_LEN_usage_notes"></a>

Length calculations do not count trailing spaces for fixed\-length character strings but do count them for variable\-length strings\. 

## Example<a name="r_LEN-example"></a>

The following example returns the number of bytes and the number of characters in the string `français`\.

```
select octet_length('français'), 
len('français');

octet_length  | len
--------------+-----
           9  |   8
(1 row)
```

The following example returns the number of characters in the strings `cat` with no trailing spaces and `cat ` with three trailing spaces: 

```
select len('cat'), len('cat   ');
 len | len
-----+-----
   3 |   6
(1 row)
```

The following example returns the ten longest VENUENAME entries in the VENUE table: 

```
select venuename, len(venuename)
from venue
order by 2 desc, 1
limit 10;

venuename                               | len
----------------------------------------+-----
Saratoga Springs Performing Arts Center |  39
Lincoln Center for the Performing Arts  |  38
Nassau Veterans Memorial Coliseum       |  33
Jacksonville Municipal Stadium          |  30
Rangers BallPark in Arlington           |  29
University of Phoenix Stadium           |  29
Circle in the Square Theatre            |  28
Hubert H. Humphrey Metrodome            |  28
Oriole Park at Camden Yards             |  27
Dick's Sporting Goods Park              |  26
(10 rows)
```