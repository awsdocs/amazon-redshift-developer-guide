# BPCHARCMP function<a name="r_BPCHARCMP"></a>

Compares the value of two strings and returns an integer\. If the strings are identical, returns 0\. If the first string is "greater" alphabetically, returns 1\. If the second string is "greater", returns \-1\. 

For multibyte characters, the comparison is based on the byte encoding\.

Synonym of [BTTEXT\_PATTERN\_CMP function](r_BTTEXT_PATTERN_CMP.md)\. 

## Syntax<a name="r_BPCHARCMP-synopsis"></a>

```
BPCHARCMP(string1, string2)
```

## Arguments<a name="r_BPCHARCMP-arguments"></a>

 *string1*   
The first input parameter is a CHAR or VARCHAR string\. 

 *string2*   
The second parameter is a CHAR or VARCHAR string\. 

## Return type<a name="r_BPCHARCMP-return-type"></a>

The BPCHARCMP function returns an integer\. 

## Examples<a name="r_BPCHARCMP-examples"></a>

The following example determines whether a user's first name is alphabetically greater than the user's last name for the first ten entries in USERS: 

```
select userid, firstname, lastname,
bpcharcmp(firstname, lastname)
from users
order by 1, 2, 3, 4
limit 10;
```

This example returns the following sample output: 

```
 userid | firstname | lastname  | bpcharcmp
--------+-----------+-----------+-----------
      1 | Rafael    | Taylor    |        -1
      2 | Vladimir  | Humphrey  |         1
      3 | Lars      | Ratliff   |        -1
      4 | Barry     | Roy       |        -1
      5 | Reagan    | Hodge     |         1
      6 | Victor    | Hernandez |         1
      7 | Tamekah   | Juarez    |         1
      8 | Colton    | Roy       |        -1
      9 | Mufutau   | Watkins   |        -1
     10 | Naida     | Calderon  |         1
(10 rows)
```

You can see that for entries where the string for the FIRSTNAME is later alphabetically than the LASTNAME, BPCHARCMP returns 1\. If the LASTNAME is alphabetically later than FIRSTNAME, BPCHARCMP returns \-1\. 

This example returns all entries in the USER table whose FIRSTNAME is identical to their LASTNAME: 

```
select userid, firstname, lastname,
bpcharcmp(firstname, lastname)
from users where bpcharcmp(firstname, lastname)=0
order by 1, 2, 3, 4;


userid | firstname | lastname | bpcharcmp
-------+-----------+----------+-----------
    62 | Chase     | Chase    |         0
  4008 | Whitney   | Whitney  |         0
 12516 | Graham    | Graham   |         0
 13570 | Harper    | Harper   |         0
 16712 | Cooper    | Cooper   |         0
 18359 | Chase     | Chase    |         0
 27530 | Bradley   | Bradley  |         0
 31204 | Harding   | Harding  |         0
(8 rows)
```