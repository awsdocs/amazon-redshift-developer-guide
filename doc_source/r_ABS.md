# ABS function<a name="r_ABS"></a>

 ABS calculates the absolute value of a number, where that number can be a literal or an expression that evaluates to a number\. 

## Syntax<a name="r_ABS-synopsis"></a>

```
ABS (number)
```

## Arguments<a name="r_ABS-arguments"></a>

 *number*   
Number or expression that evaluates to a number\. 

## Return type<a name="r_ABS-return-type"></a>

ABS returns the same data type as its argument\. 

## Examples<a name="r_ABS-examples"></a>

Calculate the absolute value of \-38: 

```
select abs (-38);
abs
-------
38
(1 row)
```

Calculate the absolute value of \(14\-76\): 

```
select abs (14-76);
abs
-------
62
(1 row)
```