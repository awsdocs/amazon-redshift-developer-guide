# REGEXP\_INSTR Function<a name="REGEXP_INSTR"></a>

<<<<<<< HEAD
Searches a string for a regular expression pattern and returns an integer that indicates the beginning position or ending position of the matched substring\. If no match is found, then the function returns 0\. REGEXP\_INSTR is similar to the [POSITION](r_POSITION.md) function, but lets you search a string for a regular expression pattern\. For more information about regular expressions, see [POSIX Operators](pattern-matching-conditions-posix.md)\.
=======
Searches a string for a regular expression pattern and returns an integer that indicates the beginning position of the matched substring\. If no match is found, then the function returns 0\. REGEXP\_INSTR is similar to the [POSITION Function](r_POSITION.md), but lets you search a string for a regular expression pattern\. For more information about regular expressions, see [POSIX Operators](pattern-matching-conditions-posix.md)\.
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d

## Syntax<a name="REGEXP_INSTR-synopsis"></a>

```
REGEXP_INSTR ( source_string, pattern [, position ] )
```

## Arguments<a name="REGEXP_INSTR-arguments"></a>

 *source\_string*   
A string expression, such as a column name, to be searched\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\.

 *position*   
A positive integer that indicates the position within *source\_string* to begin searching\. The position is based on the number of characters, not bytes, so that multibyte characters are counted as single characters\. The default is 1\. If *position* is less than 1, the search begins at the first character of *source\_string*\. If *position* is greater than the number of characters in *source\_string*, the result is 0\.

## Return Type<a name="REGEXP_INSTR-return-type"></a>

Integer

## Example<a name="REGEXP_INSTR-examples"></a>

The following example searches for the `@` character that begins a domain name and returns the starting position of the first match\.

```
select email, regexp_instr(email,'@[^.]*') 
from users 
limit 5;
                  
email                                 | regexp_instr
--------------------------------------+-------------
Cum@accumsan.com                      |            4
lorem.ipsum@Vestibulumante.com        |           12
non.justo.Proin@ametconsectetuer.edu  |           16
non.ante.bibendum@porttitortellus.org |           18
eros@blanditatnisi.org                |            5
(5 rows)
```

The following example searches for variants of the word `Center` and returns the starting position of the first match\.

```
select venuename, regexp_instr(venuename,'[cC]ent(er|re)$') 
from venue 
where regexp_instr(venuename,'[cC]ent(er|re)$') > 0 
limit 5;

venuename             | regexp_instr
----------------------+-------------
The Home Depot Center |           16
Izod Center           |            6
Wachovia Center       |           10
Air Canada Centre     |           12
United Center         |            8
```