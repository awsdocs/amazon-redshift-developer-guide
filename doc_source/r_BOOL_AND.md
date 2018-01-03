# BOOL\_AND Function<a name="r_BOOL_AND"></a>

## Syntax<a name="r_BOOL_AND-synopsis"></a>

```
BOOL_AND ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BOOL_AND-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have a BOOLEAN or integer data type\. The return type of the function is BOOLEAN\.

DISTINCT | ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. See [DISTINCT Support for Bit\-Wise Aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.