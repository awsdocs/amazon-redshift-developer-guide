# LISTAGG window function<a name="r_WF_LISTAGG"></a>

For each group in a query, the LISTAGG window function orders the rows for that group according to the ORDER BY expression, then concatenates the values into a single string\. 

LISTAGG is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_WF_LISTAGG-synopsis"></a>

```
LISTAGG( [DISTINCT] expression [, 'delimiter' ] ) 
[ WITHIN GROUP (ORDER BY order_list) ] 
OVER ( [PARTITION BY partition_expression] )
```

## Arguments<a name="r_WF_LISTAGG-arguments"></a>

DISTINCT  
\(Optional\) A clause that eliminates duplicate values from the specified expression before concatenating\. Trailing spaces are ignored, so the strings `'a'` and `'a '` are treated as duplicates\. LISTAGG uses the first value encountered\. For more information, see [Significance of trailing blanks](r_Character_types.md#r_Character_types-significance-of-trailing-blanks)\.

  

*aggregate\_expression*   
 Any valid expression \(such as a column name\) that provides the values to aggregate\. NULL values and empty strings are ignored\. 

 *delimiter*   
\(Optional\) The string constant to will separate the concatenated values\. The default is NULL\.

 WITHIN GROUP \(ORDER BY *order\_list*\)   
\(Optional\) A clause that specifies the sort order of the aggregated values\. Deterministic only if ORDER BY provides unique ordering\. The default is to aggregate all rows and return a single value\.

 OVER   
 A clause that specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

 PARTITION BY *partition\_expression*   
\(Optional\) Sets the range of records for each group in the OVER clause\.

## Returns<a name="r_WF_LISTAGG-data-types"></a>

VARCHAR\(MAX\)\. If the result set is larger than the maximum VARCHAR size \(64K – 1, or 65535\), then LISTAGG returns the following error:

```
Invalid operation: Result size exceeds LISTAGG limit
```

## Examples<a name="r_WF_LISTAGG-examples"></a>

See [LISTAGG window function examples](r_Examples_of_LISTAGG_WF.md)\.