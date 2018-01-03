# PERCENTILE\_DISC Window Function<a name="r_WF_PERCENTILE_DISC"></a>

PERCENTILE\_DISC is an inverse distribution function that assumes a discrete distribution model\. It takes a percentile value and a sort specification and returns an element from the given set\. 

For a given percentile value P, PERCENTILE\_DISC sorts the values of the expression in the ORDER BY clause and returns the value with the smallest cumulative distribution value \(with respect to the same sort specification\) that is greater than or equal to P\. 

You can specify only the PARTITION clause in the OVER clause\. 

## Syntax<a name="r_WF_PERCENTILE_DISC-synopsis"></a>

```
PERCENTILE_DISC ( percentile )
WITHIN GROUP (ORDER BY expr)
OVER (  [ PARTITION BY expr_list ]  )
```

## Arguments<a name="r_WF_PERCENTILE_DISC-arguments"></a>

 *percentile*   
Numeric constant between 0 and 1\. Nulls are ignored in the calculation\.

WITHIN GROUP \( ORDER BY *expr*\)   
Specifies numeric or date/time values to sort and compute the percentile over\. 

OVER   
Specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

PARTITION BY *expr*   
Optional argument that sets the range of records for each group in the OVER clause\.

## Returns<a name="r_WF_PERCENTILE_DISC-returns"></a>

The same data type as the ORDER BY expression in the WITHIN GROUP clause\.

## Examples<a name="r_WF_PERCENTILE_DISC-examples"></a>

See [PERCENTILE\_DISC Window Function Examples](r_Examples_of_PERCENTILE_DISC_WF.md)\. 