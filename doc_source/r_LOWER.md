# LOWER function<a name="r_LOWER"></a>

Converts a string to lowercase\. LOWER supports UTF\-8 multibyte characters, up to a maximum of four bytes per character\.

## Syntax<a name="r_LOWER-synopsis"></a>

```
LOWER(string)
```

## Argument<a name="r_LOWER-argument"></a>

 *string*   
The input parameter is a CHAR or VARCHAR string\. 

## Return type<a name="r_LOWER-return-type"></a>

The LOWER function returns a character string that is the same data type as the input string \(CHAR or VARCHAR\)\.

## Examples<a name="r_LOWER-examples"></a>

The following example converts the CATNAME field to lowercase: 

```
select catname, lower(catname) from category order by 1,2;

 catname  |   lower
----------+-----------
Classical | classical
Jazz      | jazz
MLB       | mlb
MLS       | mls
Musicals  | musicals
NBA       | nba
NFL       | nfl
NHL       | nhl
Opera     | opera
Plays     | plays
Pop       | pop
(11 rows)
```