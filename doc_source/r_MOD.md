# MOD function<a name="r_MOD"></a>

Returns the remainder of two numbers, otherwise known as a *modulo* operation\. To calculate the result, the first parameter is divided by the second\.

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

## Usage notes<a name="r_MOD-usage-notes"></a>

You can use *%* as a modulo operator\.

## Examples<a name="r_MOD-example"></a>

Some examples use the *TICKIT* sample data set\. For more information, see [Sample database](https://docs.aws.amazon.com/redshift/latest/dg/c_sampledb.html)\.

The following example return the remainder when a number is divided by another:

```
SELECT MOD(10, 4);
               
 mod
------
 2
```

The following example returns a decimal result:

```
SELECT MOD(10.5, 4);
               
 mod
------
 2.5
```

You can cast parameter values:

```
SELECT MOD(CAST(16.4 as integer), 5);
               
 mod
------
 1
```

Check if the first parameter is even by dividing it by 2:

```
SELECT mod(5,2) = 0 as is_even;
               
 is_even
--------
 false
```

You can use the *%* as a modulo operator:

```
SELECT 11 % 4 as remainder;
               
 remainder
-----------
 3
```

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