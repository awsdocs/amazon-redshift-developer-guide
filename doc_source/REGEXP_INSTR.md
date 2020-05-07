# REGEXP\_INSTR function<a name="REGEXP_INSTR"></a>

Searches a string for a regular expression pattern and returns an integer that indicates the beginning position or ending position of the matched substring\. If no match is found, then the function returns 0\. REGEXP\_INSTR is similar to the [POSITION](r_POSITION.md) function, but lets you search a string for a regular expression pattern\. For more information about regular expressions, see [POSIX operators](pattern-matching-conditions-posix.md)\.

## Syntax<a name="REGEXP_INSTR-synopsis"></a>

```
REGEXP_INSTR ( source_string, pattern [, position [, occurrence] [, option [, parameters ] ] ] ] )
```

## Arguments<a name="REGEXP_INSTR-arguments"></a>

 *source\_string*   
A string expression, such as a column name, to be searched\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\.

 *position*   
A positive integer that indicates the position within *source\_string* to begin searching\. The position is based on the number of characters, not bytes, so that multibyte characters are counted as single characters\. The default is 1\. If *position* is less than 1, the search begins at the first character of *source\_string*\. If *position* is greater than the number of characters in *source\_string*, the result is 0\.

 *occurrence*   
A positive integer that indicates which occurrence of the pattern to use\. REGEXP\_INSTR skips the first *occurrence* \-1 matches\. The default is 1\. If *occurrence* is less than 1 or greater than the number of characters in *source\_string*, the search is ignored and the result is 0\.

 *option*   
A value that indicates whether to return the position of the first character of the match \(`0`\) or the position of the first character following the end of the match \(`1`\)\. A nonzero value is the same as 1\. The default value is 0\. 

 *parameters*   
One or more string literals that indicate how the function matches the pattern\. The possible values are the following:  
+ c – Perform case\-sensitive matching\. The default is to use case\-sensitive matching\. 
+ i – Perform case\-insensitive matching\. 
+ e – Extract a substring using a subexpression\. 

  If *pattern* includes a subexpression, REGEXP\_INSTR matches a substring using the first subexpression in *pattern*\. REGEXP\_INSTR considers only the first subexpression; additional subexpressions are ignored\. If the pattern doesn't have a subexpression, REGEXP\_INSTR ignores the 'e' parameter\. 

## Return type<a name="REGEXP_INSTR-return-type"></a>

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