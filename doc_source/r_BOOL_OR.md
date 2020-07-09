# BOOL\_OR function<a name="r_BOOL_OR"></a>

The BOOL\_OR function operates on a single Boolean or integer column or expression\. This function applies similar logic to the BIT\_AND and BIT\_OR functions\. For this function, the return type is a Boolean value \(`true` or `false`\)\.

If any value in a set is `true`, the BOOL\_OR function returns `true` \(`t`\)\. If no value in a set is `true`, the function returns `false` \(`f`\)\.

## Syntax<a name="r_BOOL_OR-synopsis"></a>

```
BOOL_OR ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BOOL_OR-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have a BOOLEAN or integer data type\. The return type of the function is BOOLEAN\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. See [DISTINCT support for bit\-wise aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.

## Examples<a name="r_bool_or_example"></a>

You can use the Boolean functions with either Boolean expressions or integer expressions\. For example, the following query return results from the standard USERS table in the TICKIT database, which has several Boolean columns\.

The BOOL\_OR function returns `true` for all five rows\. At least one user in each of those states likes sports\.

```
select state, bool_or(likesports) from users 
group by state order by state limit 5;

state | bool_or 
------+--------
AB    | t      
AK    | t      
AL    | t       
AZ    | t       
BC    | t       
(5 rows)
```