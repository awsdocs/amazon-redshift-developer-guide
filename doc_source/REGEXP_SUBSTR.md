# REGEXP\_SUBSTR function<a name="REGEXP_SUBSTR"></a>

Returns the characters extracted from a string by searching for a regular expression pattern\. REGEXP\_SUBSTR is similar to the [SUBSTRING function](r_SUBSTRING.md) function, but lets you search a string for a regular expression pattern\. For more information about regular expressions, see [POSIX operators](pattern-matching-conditions-posix.md)\.

## Syntax<a name="REGEXP_SUBSTR-synopsis"></a>

```
REGEXP_SUBSTR ( source_string, pattern [, position [, occurrence [, parameters ] ] ] )
```

## Arguments<a name="REGEXP_SUBSTR-arguments"></a>

 *source\_string*   
A string expression, such as a column name, to be searched\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\.

 *position*   
A positive integer that indicates the position within *source\_string* to begin searching\. The position is based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\. The default is 1\. If *position* is less than 1, the search begins at the first character of *source\_string*\. If *position* is greater than the number of characters in *source\_string*, the result is an empty string \(""\)\.

 *occurrence*   
A positive integer that indicates which occurrence of the pattern to use\. REGEXP\_SUBSTR skips the first *occurrence* \-1 matches\. The default is 1\. If *occurrence* is less than 1 or greater than the number of characters in *source\_string*, the search is ignored and the result is NULL\.

 *parameters*   
One or more string literals that indicate how the function matches the pattern\. The possible values are the following:  
+ c – Perform case\-sensitive matching\. The default is to use case\-sensitive matching\. 
+ i – Perform case\-insensitive matching\. 
+ e – Extract a substring using a subexpression\. 

  If *pattern* includes a subexpression, REGEXP\_SUBSTR matches a substring using the first subexpression in *pattern*\. REGEXP\_SUBSTR considers only the first subexpression; additional subexpressions are ignored\. If the pattern doesn't have a subexpression, REGEXP\_SUBSTR ignores the 'e' parameter\. 
+ p – Interpret the pattern with Perl Compatible Regular Expression \(PCRE\) dialect\.

## Return type<a name="REGEXP_SUBSTR-return-type"></a>

VARCHAR

## Example<a name="REGEXP_SUBSTR-examples"></a>

The following example returns the portion of an email address between the @ character and the domain extension\.

```
SELECT email, regexp_substr(email,'@[^.]*')
FROM users
ORDER BY userid LIMIT 4;

                     email                     |      regexp_substr
-----------------------------------------------+--------------------------
 Etiam.laoreet.libero@sodalesMaurisblandit.edu | @sodalesMaurisblandit
 Suspendisse.tristique@nonnisiAenean.edu       | @nonnisiAenean
 amet.faucibus.ut@condimentumegetvolutpat.ca   | @condimentumegetvolutpat
 sed@lacusUtnec.ca                             | @lacusUtnec
```

The following example returns the portion of the input corresponding to the first occurrence of the string `FOX`, using case\-insensitive matching\.

```
SELECT regexp_substr('the fox', 'FOX', 1, 1, 'i');

 regexp_substr
---------------
 fox
```

The following example uses a pattern written in the PCRE dialect to locate words containing at least one number and one lowercase letter\. It uses the `?=` operator, which has a specific look\-ahead connotation in PCRE\. This example returns the portion of the input corresponding to the second such word\.

```
SELECT regexp_substr('passwd7 plain A1234 a1234', '(?=[^ ]*[a-z])(?=[^ ]*[0-9])[^ ]+', 1, 2, 'p');

 regexp_substr
---------------
 a1234
```

The following example uses a pattern written in the PCRE dialect to locate words containing at least one number and one lowercase letter\. It uses the `?=` operator, which has a specific look\-ahead connotation in PCRE\. This example returns the portion of the input corresponding to the second such word, but differs from the previous example in that it uses case\-insensitive matching\.

```
SELECT regexp_substr('passwd7 plain A1234 a1234', '(?=[^ ]*[a-z])(?=[^ ]*[0-9])[^ ]+', 1, 2, 'ip');

 regexp_substr
---------------
 A1234
```