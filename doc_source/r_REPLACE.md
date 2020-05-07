# REPLACE function<a name="r_REPLACE"></a>

Replaces all occurrences of a set of characters within an existing string with other specified characters\. 

REPLACE is similar to the [TRANSLATE function](r_TRANSLATE.md) and the [REGEXP\_REPLACE function](REGEXP_REPLACE.md), except that TRANSLATE makes multiple single\-character substitutions and REGEXP\_REPLACE lets you search a string for a regular expression pattern, while REPLACE substitutes one entire string with another string\.

## Syntax<a name="r_REPLACE-synopsis"></a>

```
REPLACE(string1, old_chars, new_chars)
```

## Arguments<a name="r_REPLACE-arguments"></a>

 *string*   
CHAR or VARCHAR string to be searched search 

 *old\_chars*   
CHAR or VARCHAR string to replace\. 

 *new\_chars*   
New CHAR or VARCHAR string replacing the *old\_string*\. 

## Return type<a name="r_REPLACE-return-type"></a>

VARCHAR

If either *old\_chars* or *new\_chars* is NULL, the return is NULL\. 

## Examples<a name="r_REPLACE-examples"></a>

The following example converts the string `Shows` to `Theatre` in the CATGROUP field: 

```
select catid, catgroup,
replace(catgroup, 'Shows', 'Theatre')
from category
order by 1,2,3;

 catid | catgroup | replace
-------+----------+----------
     1 | Sports   | Sports
     2 | Sports   | Sports
     3 | Sports   | Sports
     4 | Sports   | Sports
     5 | Sports   | Sports
     6 | Shows    | Theatre
     7 | Shows    | Theatre
     8 | Shows    | Theatre
     9 | Concerts | Concerts
    10 | Concerts | Concerts
    11 | Concerts | Concerts
(11 rows)
```