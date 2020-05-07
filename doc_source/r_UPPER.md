# UPPER function<a name="r_UPPER"></a>

Converts a string to uppercase\. UPPER supports UTF\-8 multibyte characters, up to a maximum of four bytes per character\.

## Syntax<a name="r_UPPER-synopsis"></a>

```
UPPER(string)
```

## Arguments<a name="r_UPPER-arguments"></a>

 *string*   
The input parameter is a CHAR or VARCHAR string\. 

## Return type<a name="r_UPPER-return-type"></a>

The UPPER function returns a character string that is the same data type as the input string \(CHAR or VARCHAR\)\. 

## Examples<a name="r_UPPER-examples"></a>

The following example converts the CATNAME field to uppercase: 

```
select catname, upper(catname) from category order by 1,2;

 catname  |   upper
----------+-----------
Classical | CLASSICAL
Jazz      | JAZZ
MLB       | MLB
MLS       | MLS
Musicals  | MUSICALS
NBA       | NBA
NFL       | NFL
NHL       | NHL
Opera     | OPERA
Plays     | PLAYS
Pop       | POP
(11 rows)
```