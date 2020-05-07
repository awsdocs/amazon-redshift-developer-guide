# RTRIM function<a name="r_RTRIM"></a>

The RTRIM function trims a specified set of characters from the end of a string\. 

## Syntax<a name="r_RTRIM-synopsis"></a>

```
RTRIM( string, trim_chars )
```

## Arguments<a name="r_RTRIM-arguments"></a>

 *string*   
The string column or expression to be trimmed\.

 *trim\_chars*   
A string column or expression representing the characters to be trimmed from the end of *string*\.

## Return type<a name="r_RTRIM-return-type"></a>

A string that is the same data type as the *string* argument\.

## Example<a name="r_RTRIM-example"></a>

The following example trims the characters 'Park' from the end of VENUENAME where present: 

```
select venueid, venuename, rtrim(venuename, 'Park')
from venue
order by 1, 2, 3
limit 10;

venueid |         venuename          |          rtrim
--------+----------------------------+-------------------------
      1 | Toyota Park                | Toyota
      2 | Columbus Crew Stadium      | Columbus Crew Stadium
      3 | RFK Stadium                | RFK Stadium
      4 | CommunityAmerica Ballpark  | CommunityAmerica Ballp
      5 | Gillette Stadium           | Gillette Stadium
      6 | New York Giants Stadium    | New York Giants Stadium
      7 | BMO Field                  | BMO Field
      8 | The Home Depot Center      | The Home Depot Cente
      9 | Dick's Sporting Goods Park | Dick's Sporting Goods
     10 | Pizza Hut Park             | Pizza Hut
(10 rows)
```

Note that RTRIM removes any of the characters `P`, `a`, `r`, or `k` when they appear at the end of a VENUENAME\. 