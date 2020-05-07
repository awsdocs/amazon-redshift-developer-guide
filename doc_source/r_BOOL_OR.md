# BOOL\_OR function<a name="r_BOOL_OR"></a>

## Syntax<a name="r_BOOL_OR-synopsis"></a>

```
BOOL_OR ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BOOL_OR-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have a BOOLEAN or integer data type\. The return type of the function is BOOLEAN\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. See [DISTINCT support for bit\-wise aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.