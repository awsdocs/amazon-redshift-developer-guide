# TAN function<a name="r_TAN"></a>

TAN is a trigonometric function that returns the tangent of a number\. The input parameter must be a non\-zero number \(in radians\)\. 

## Syntax<a name="r_TAN-synopsis"></a>

```
TAN(number)
```

## Argument<a name="r_TAN-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_TAN-return-type"></a>

The TAN function returns a double precision number\. 

## Examples<a name="r_TAN-examples"></a>

The following example returns the tangent of 0: 

```
select tan(0);
tan
-----
0
(1 row)
```