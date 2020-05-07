# BIT\_AND function<a name="r_BIT_AND"></a>

## Syntax<a name="r_BIT_AND-synopsis"></a>

```
BIT_AND ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BIT_AND-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have an INT, INT2, or INT8 data type\. The function returns an equivalent INT, INT2, or INT8 data type\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. See [DISTINCT support for bit\-wise aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.