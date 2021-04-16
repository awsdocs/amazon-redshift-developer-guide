# \|\| \(Concatenation\) operator<a name="r_concat_op"></a>

Concatenates two strings on either side of the \|\| symbol and returns the concatenated string\. 

Similar to [CONCAT](r_CONCAT.md)\. 

**Note**  
For both the CONCAT function and the concatenation operator, if one or both strings is null, the result of the concatenation is null\. 

## Syntax<a name="r_concat_op-synopsis"></a>

```
string1 || string2
```

## Arguments<a name="r_concat_op-arguments"></a>

 *string1*, *string2*   
Both arguments can be fixed\-length or variable\-length character strings or expressions\. 

## Return type<a name="r_concat_op-return-type"></a>

 The \|\| operator returns a string\. The type of string is the same as the input arguments\. 

## Example<a name="r_concat_op-example"></a>

The following example concatenates the FIRSTNAME and LASTNAME fields from the USERS table: 

```
select firstname || ' ' || lastname
from users
order by 1
limit 10;

?column?
-----------------
Aaron Banks
Aaron Booth
Aaron Browning
Aaron Burnett
Aaron Casey
Aaron Cash
Aaron Castro
Aaron Dickerson
Aaron Dixon
Aaron Dotson
(10 rows)
```

 To concatenate columns that might contain nulls, use the [NVL expression](r_NVL_function.md) expression\. The following example uses NVL to return a 0 whenever NULL is encountered\. 

```
select venuename || ' seats ' || nvl(venueseats, 0) 
from venue where venuestate = 'NV' or venuestate = 'NC'
order by 1
limit 10;

seating                            
-----------------------------------
Ballys Hotel seats 0               
Bank of America Stadium seats 73298
Bellagio Hotel seats 0             
Caesars Palace seats 0             
Harrahs Hotel seats 0              
Hilton Hotel seats 0               
Luxor Hotel seats 0                
Mandalay Bay Hotel seats 0         
Mirage Hotel seats 0               
New York New York seats 0
```