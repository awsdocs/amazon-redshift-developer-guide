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
+ p – Interpret the pattern with Perl Compatible Regular Expression \(PCRE\) dialect\.

## Return type<a name="REGEXP_INSTR-return-type"></a>

Integer

## Example<a name="REGEXP_INSTR-examples"></a>

The following example searches for the `@` character that begins a domain name and returns the starting position of the first match\.

```
SELECT email, regexp_instr(email, '@[^.]*')
FROM users
ORDER BY userid LIMIT 4;

                     email                     | regexp_instr
-----------------------------------------------+--------------
 Etiam.laoreet.libero@sodalesMaurisblandit.edu |           21
 Suspendisse.tristique@nonnisiAenean.edu       |           22
 amet.faucibus.ut@condimentumegetvolutpat.ca   |           17
 sed@lacusUtnec.ca                             |            4
```

The following example searches for variants of the word `Center` and returns the starting position of the first match\.

```
SELECT venuename, regexp_instr(venuename,'[cC]ent(er|re)$')
FROM venue
WHERE regexp_instr(venuename,'[cC]ent(er|re)$') > 0
ORDER BY venueid LIMIT 4;

       venuename       | regexp_instr
-----------------------+--------------
 The Home Depot Center |           16
 Izod Center           |            6
 Wachovia Center       |           10
 Air Canada Centre     |           12
```

The following example finds the starting position of the first occurrence of the string `FOX`, using case\-insensitive matching logic\. 

```
SELECT regexp_instr('the fox', 'FOX', 1, 1, 0, 'i');

 regexp_instr
 --------------
            5
```

The following example uses a pattern written in PCRE dialect to locate words containing at least one number and one lowercase letter\. It uses the `?=` operator, which has a specific look\-ahead connotation in PCRE\. This example finds the starting position of the second such word\.

```
SELECT regexp_instr('passwd7 plain A1234 a1234', '(?=[^ ]*[a-z])(?=[^ ]*[0-9])[^ ]+', 1, 2, 0, 'p');

 regexp_instr
 --------------
           21
```

The following example uses a pattern written in PCRE dialect to locate words containing at least one number and one lowercase letter\. It uses the `?=` operator, which has a specific look\-ahead connotation in PCRE\. This example finds the starting position of the second such word, but differs from the previous example in that it uses case\-insensitive matching\.

```
SELECT regexp_instr('passwd7 plain A1234 a1234', '(?=[^ ]*[a-z])(?=[^ ]*[0-9])[^ ]+', 1, 2, 0, 'ip');

 regexp_instr
 --------------
           15
```