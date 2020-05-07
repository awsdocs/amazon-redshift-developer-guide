# REGEXP\_REPLACE function<a name="REGEXP_REPLACE"></a>

Searches a string for a regular expression pattern and replaces every occurrence of the pattern with the specified string\. REGEXP\_REPLACE is similar to the [REPLACE function](r_REPLACE.md), but lets you search a string for a regular expression pattern\. For more information about regular expressions, see [POSIX operators](pattern-matching-conditions-posix.md)\.

REGEXP\_REPLACE is similar to the [TRANSLATE function](r_TRANSLATE.md) and the [REPLACE function](r_REPLACE.md), except that TRANSLATE makes multiple single\-character substitutions and REPLACE substitutes one entire string with another string, while REGEXP\_REPLACE lets you search a string for a regular expression pattern\.

## Syntax<a name="REGEXP_REPLACE-synopsis"></a>

```
REGEXP_REPLACE ( source_string, pattern [, replace_string [ , position ] ] )
```

## Arguments<a name="REGEXP_REPLACE-arguments"></a>

 *source\_string*   
A string expression, such as a column name, to be searched\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\. 

*replace\_string*  
A string expression, such as a column name, that will replace each occurrence of pattern\. The default is an empty string \( "" \)\. 

 *position*   
A positive integer that indicates the position within *source\_string* to begin searching\. The position is based on the number of characters, not bytes, so that multibyte characters are counted as single characters\. The default is 1\. If *position* is less than 1, the search begins at the first character of *source\_string*\. If *position* is greater than the number of characters in *source\_string*, the result is *source\_string*\.

## Return type<a name="REGEXP_REPLACE-return-type"></a>

VARCHAR

If either *pattern* or *replace\_string* is NULL, the return is NULL\.

## Example<a name="REGEXP_REPLACE-examples"></a>

The following example deletes the `@` and domain name from email addresses\.

```
select email, regexp_replace( email, '@.*\\.(org|gov|com)$') 
from users limit 5; 

              email                | regexp_replace 
-----------------------------------+----------------  
  DonecFri@semperpretiumneque.com  | DonecFri
  mk1wait@UniOfTech.org            | mk1wait
  sed@redshiftemails.com           | sed
  bunyung@integermath.gov          | bunyung
  tomsupporter@galaticmess.org     | tomsupporter
```

The following example selects URLs from the fictional WEBSITES table and replaces the domain names with this value: `internal.company.com/`

```
select url, regexp_replace(url, '^.*\\.[[:alpha:]]{3}/', 
'internal.company.com/') from websites limit 4;

  url                                                                                          
-----------------------------------------------------
|  regexp_replace                       
+-----------------------------------------------------
example.com/cuisine/locations/home.html    
| internal.company.com/cuisine/locations/home.html  

anycompany.employersthere.com/employed/A/index.html   
| internal.company.com/employed/A/index.html  

example.gov/credentials/keys/public      
| internal.company.com/credentials/keys/public  

yourcompany.com/2014/Q1/summary.pdf          
| internal.company.com/2014/Q1/summary.pdf
```