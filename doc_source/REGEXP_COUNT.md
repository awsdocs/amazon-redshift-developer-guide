# REGEXP\_COUNT function<a name="REGEXP_COUNT"></a>

Searches a string for a regular expression pattern and returns an integer that indicates the number of times the pattern occurs in the string\. If no match is found, then the function returns 0\. For more information about regular expressions, see [POSIX operators](pattern-matching-conditions-posix.md)\.

## Syntax<a name="REGEXP_COUNT-synopsis"></a>

```
REGEXP_COUNT ( source_string, pattern [, position ] )
```

## Arguments<a name="REGEXP_COUNT-arguments"></a>

 *source\_string*   
A string expression, such as a column name, to be searched\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\.

 *position*   
A positive integer that indicates the position within *source\_string* to begin searching\. The position is based on the number of characters, not bytes, so that multibyte characters are counted as single characters\. The default is 1\. If *position* is less than 1, the search begins at the first character of *source\_string*\. If *position* is greater than the number of characters in *source\_string*, the result is 0\.

## Return type<a name="REGEXP_COUNT-return-type"></a>

Integer

## Example<a name="REGEXP_COUNT-examples"></a>

The following example counts the number of times a three\-letter sequence occurs\.

```
select regexp_count('abcdefghijklmnopqrstuvwxyz', '[a-z]{3}');  

regexp_count  
--------------             
8 
(1 row)
```

The following example counts the number of times the top\-level domain name is either `org` or `edu`\. 

```
select email, regexp_count(email,'@[^.]*\\.(org|edu)') 
from users limit 5;

                    email                   | regexp_count  
--------------------------------------------+--------------  
elementum@semperpretiumneque.ca             |   0  
Integer.mollis.Integer@tristiquealiquet.org |   1  
lorem.ipsum@Vestibulumante.com              |   0  
euismod@turpis.org                          |   1  
non.justo.Proin@ametconsectetuer.edu        |   1
```