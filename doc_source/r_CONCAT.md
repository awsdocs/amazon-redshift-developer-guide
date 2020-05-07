# CONCAT<a name="r_CONCAT"></a>

The CONCAT function concatenates two character strings and returns the resulting string\. To concatenate more than two strings, use nested CONCAT functions\. The concatenation operator \(`||`\) between two strings produces the same results as the CONCAT function\. 

**Note**  
For both the CONCAT function and the concatenation operator, if one or both strings is null, the result of the concatenation is null\. 

## Syntax<a name="r_CONCAT-synopsis"></a>

```
CONCAT ( string1, string2 )
```

## Arguments<a name="r_CONCAT-arguments"></a>

 *string1*, *string2*   
Both arguments can be fixed\-length or variable\-length character strings or expressions\. 

## Return type<a name="r_CONCAT-return-type"></a>

 CONCAT returns a string\. The data type of the string is the same type as the input arguments\. 

## Examples<a name="r_CONCAT-examples"></a>

The following example concatenates two character literals: 

```
select concat('December 25, ', '2008');

concat
-------------------
December 25, 2008
(1 row)
```

The following query, using the `||` operator instead of CONCAT, produces the same result: 

```
select 'December 25, '||'2008';

?column?
-------------------
December 25, 2008
(1 row)
```

The following example uses two CONCAT functions to concatenate three character strings: 

```
select concat('Thursday, ', concat('December 25, ', '2008'));

concat
-----------------------------
Thursday, December 25, 2008
(1 row)
```

To concatenate columns that might contain nulls, use the [NVL expression](r_NVL_function.md)\. The following example uses NVL to return a 0 whenever NULL is encountered\. 

```
select concat(venuename, concat(' seats ', nvl(venueseats, 0))) as seating
from venue where venuestate = 'NV' or venuestate = 'NC'
order by 1
limit 5;

seating                            
-----------------------------------
Ballys Hotel seats 0               
Bank of America Stadium seats 73298
Bellagio Hotel seats 0             
Caesars Palace seats 0             
Harrahs Hotel seats 0              
(5 rows)
```

The following query concatenates CITY and STATE values from the VENUE table: 

```
select concat(venuecity, venuestate)
from venue
where venueseats > 75000
order by venueseats;

concat
-------------------
DenverCO
Kansas CityMO
East RutherfordNJ
LandoverMD
(4 rows)
```

The following query uses nested CONCAT functions\. The query concatenates CITY and STATE values from the VENUE table but delimits the resulting string with a comma and a space: 

```
select concat(concat(venuecity,', '),venuestate)
from venue
where venueseats > 75000
order by venueseats;

concat
---------------------
Denver, CO
Kansas City, MO
East Rutherford, NJ
Landover, MD
(4 rows)
```