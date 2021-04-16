# REPEAT function<a name="r_REPEAT"></a>

Repeats a string the specified number of times\. If the input parameter is numeric, REPEAT treats it as a string\. 

Synonym for [REPLICATE function](r_REPLICATE.md)\. 

## Syntax<a name="r_REPEAT-synopsis"></a>

```
REPEAT(string, integer)
```

## Arguments<a name="r_REPEAT-arguments"></a>

 *string*   
The first input parameter is the string to be repeated\. 

 *integer*   
The second parameter is an integer indicating the number of times to repeat the string\. 

## Return type<a name="r_REPEAT-return-type"></a>

The REPEAT function returns a string\. 

## Examples<a name="r_REPEAT-examples"></a>

The following example repeats the value of the CATID column in the CATEGORY table three times: 

```
select catid, repeat(catid,3)
from category
order by 1,2;

 catid | repeat
-------+--------
     1 | 111
     2 | 222
     3 | 333
     4 | 444
     5 | 555
     6 | 666
     7 | 777
     8 | 888
     9 | 999
    10 | 101010
    11 | 111111
(11 rows)
```