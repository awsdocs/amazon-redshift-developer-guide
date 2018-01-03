# LEAD Window Function<a name="r_WF_LEAD"></a>

 The LEAD window function returns the values for a row at a given offset below \(after\) the current row in the partition\. 

## Syntax<a name="r_WF_LEAD-synopsis"></a>

```
LEAD (value_expr [, offset ])
[ IGNORE NULLS | RESPECT NULLS ]
OVER ( [ PARTITION BY window_partition ] ORDER BY window_ordering )
```

## Arguments<a name="r_WF_LEAD-arguments"></a>

 *value\_expr*   
The target column or expression that the function operates on\. 

 *offset*   
 An optional parameter that specifies the number of rows below the current row to return values for\. The offset can be a constant integer or an expression that evaluates to an integer\. If you do not specify an offset, Amazon Redshift uses `1` as the default value\. An offset of `0` indicates the current row\. 

IGNORE NULLS   
An optional specification that indicates that Amazon Redshift should skip null values in the determination of which row to use\. Null values are included if IGNORE NULLS is not listed\.   
You can use an NVL or COALESCE expression to replace the null values with another value\. For more information, see [NVL Expression](r_NVL_function.md)\. 

RESPECT NULLS   
 Indicates that Amazon Redshift should include null values in the determination of which row to use\. RESPECT NULLS is supported by default if you do not specify IGNORE NULLS\. 

OVER   
Specifies the window partitioning and ordering\. The OVER clause cannot contain a window frame specification\. 

PARTITION BY *window\_partition*   
An optional argument that sets the range of records for each group in the OVER clause\. 

ORDER BY *window\_ordering*   
Sorts the rows within each partition\. 

The LEAD window function supports expressions that use any of the Amazon Redshift data types\. The return type is the same as the type of the *value\_expr*\. 

## Examples<a name="r_WF_LEAD-examples"></a>

See [LEAD Window Function Examples](r_Examples_of_LEAD_WF.md)\. 