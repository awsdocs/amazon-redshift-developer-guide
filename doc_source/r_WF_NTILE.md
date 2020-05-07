# NTILE window function<a name="r_WF_NTILE"></a>

 The NTILE window function divides ordered rows in the partition into the specified number of ranked groups of as equal size as possible and returns the group that a given row falls into\. 

## Syntax<a name="r_WF_NTILE-synopsis"></a>

```
NTILE (expr)
OVER ( 
[ PARTITION BY expression_list ] 
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_NTILE-arguments"></a>

 *expr*   
The number of ranking groups and must result in a positive integer value \(greater than 0\) for each partition\. The *expr* argument must not be nullable\. 

OVER   
 A clause that specifies the window partitioning and ordering\. The OVER clause cannot contain a window frame specification\. 

PARTITION BY *window\_partition*   
Optional\. The range of records for each group in the OVER clause\. 

ORDER BY *window\_ordering*   
Optional\. An expression that sorts the rows within each partition\. If the ORDER BY clause is omitted, the ranking behavior is the same\.  
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="r_WF_NTILE-return-type"></a>

BIGINT

## Examples<a name="r_WF_NTILE-examples"></a>

See [NTILE window function examples](r_Examples_of_NTILE_WF.md)\. 