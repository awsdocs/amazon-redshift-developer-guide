# MOD function<a name="r_MOD"></a>

The MOD function returns a numeric result that is the remainder of two numeric parameters\. The first parameter is divided by the second parameter\. 

## Syntax<a name="r_MOD-synopsis"></a>

```
MOD(number1, number2)
```

## Arguments<a name="r_MOD-arguments"></a>

 *number1*   
The first input parameter is an INTEGER, SMALLINT, BIGINT, or DECIMAL number\. If either parameter is a DECIMAL type, the other parameter must also be a DECIMAL type\. If either parameter is an INTEGER, the other parameter can be an INTEGER, SMALLINT, or BIGINT\. Both parameters can also be SMALLINT or BIGINT, but one parameter cannot be a SMALLINT if the other is a BIGINT\. 

 *number2*   
The second parameter is an INTEGER, SMALLINT, BIGINT, or DECIMAL number\. The same data type rules apply to *number2* as to *number1*\. 

## Return type<a name="r_MOD-return-type"></a>

Valid return types are DECIMAL, INT, SMALLINT, and BIGINT\. The return type of the MOD function is the same numeric type as the input parameters, if both input parameters are the same type\. If either input parameter is an INTEGER, however, the return type will also be an INTEGER\. 

## Example<a name="r_MOD-example"></a>

The following example returns information for odd\-numbered categories in the CATEGORY table: 

```
select catid, catname
from category
where mod(catid,2)=1
order by 1,2;

 catid |  catname
-------+-----------
     1 | MLB
     3 | NFL
     5 | MLS
     7 | Plays
     9 | Pop
    11 | Classical
(6 rows)
```