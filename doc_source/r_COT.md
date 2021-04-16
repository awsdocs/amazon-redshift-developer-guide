# COT function<a name="r_COT"></a>

COT is a trigonometric function that returns the cotangent of a number\. The input parameter must be nonzero\. 

## Syntax<a name="r_COT-synopsis"></a>

```
COT(number)
```

## Argument<a name="r_COT-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_COT-return-type"></a>

The COT function returns a double precision number\. 

## Examples<a name="r_COT-examples"></a>

The following example returns the cotangent of 1: 

```
select cot(1);
cot
-------------------
0.642092615934331
(1 row)
```