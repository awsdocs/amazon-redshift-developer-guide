# REGEXP\_COUNT function<a name="REGEXP_COUNT"></a>

Searches a string for a regular expression pattern and returns an integer that indicates the number of times the pattern occurs in the string\. If no match is found, then the function returns 0\. For more information about regular expressions, see [POSIX operators](pattern-matching-conditions-posix.md)\.

## Syntax<a name="REGEXP_COUNT-synopsis"></a>

```
REGEXP_COUNT ( source_string, pattern [, position [, parameters ] ] )
```

## Arguments<a name="REGEXP_COUNT-arguments"></a>

 *source\_string*   
A string expression, such as a column name, to be searched\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\.

 *position*   
A positive integer that indicates the position within *source\_string* to begin searching\. The position is based on the number of characters, not bytes, so that multibyte characters are counted as single characters\. The default is 1\. If *position* is less than 1, the search begins at the first character of *source\_string*\. If *position* is greater than the number of characters in *source\_string*, the result is 0\.

 *parameters*   
One or more string literals that indicate how the function matches the pattern\. The possible values are the following:  
+ c – Perform case\-sensitive matching\. The default is to use case\-sensitive matching\.
+ i – Perform case\-insensitive matching\.
+ p – Interpret the pattern with Perl Compatible Regular Expression \(PCRE\) dialect\.

## Return type<a name="REGEXP_COUNT-return-type"></a>

Integer

## Example<a name="REGEXP_COUNT-examples"></a>

The following example counts the number of times a three\-letter sequence occurs\.

```
SELECT regexp_count('abcdefghijklmnopqrstuvwxyz', '[a-z]{3}');  

 regexp_count
 --------------
            8
```

The following example counts the number of times the top\-level domain name is either `org` or `edu`\. 

```
SELECT email, regexp_count(email,'@[^.]*\\.(org|edu)')FROM users
ORDER BY userid LIMIT 4;

                     email                     | regexp_count
-----------------------------------------------+--------------
 Etiam.laoreet.libero@sodalesMaurisblandit.edu |            1
 Suspendisse.tristique@nonnisiAenean.edu       |            1
 amet.faucibus.ut@condimentumegetvolutpat.ca   |            0
 sed@lacusUtnec.ca                             |            0
```

The following example counts the occurrences of the string `FOX`, using case\-insensitive matching\.

```
SELECT regexp_count('the fox', 'FOX', 1, 'i');

 regexp_count
 --------------
            1
```

The following example uses a pattern written in the PCRE dialect to locate words containing at least one number and one lowercase letter\. It uses the `?=` operator, which has a specific look\-ahead connotation in PCRE\. This example counts the number of occurrences of such words, with case\-sensitive matching\.  

```
SELECT regexp_count('passwd7 plain A1234 a1234', '(?=[^ ]*[a-z])(?=[^ ]*[0-9])[^ ]+', 1, 'p');

 regexp_count
 --------------
            2
```

The following example uses a pattern written in the PCRE dialect to locate words containing at least one number and one lowercase letter\. It uses the `?=` operator, which has a specific connotation in PCRE\. This example counts the number of occurrences of such words, but differs from the previous example in that it uses case\-insensitive matching\.

```
SELECT regexp_count('passwd7 plain A1234 a1234', '(?=[^ ]*[a-z])(?=[^ ]*[0-9])[^ ]+', 1, 'ip');

 regexp_count
 --------------
            3
```