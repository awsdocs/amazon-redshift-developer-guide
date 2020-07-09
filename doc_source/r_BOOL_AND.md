# BOOL\_AND function<a name="r_BOOL_AND"></a>

The BOOL\_AND function operates on a single Boolean or integer column or expression\. This function applies similar logic to the BIT\_AND and BIT\_OR functions\. For this function, the return type is a Boolean value \(`true` or `false`\)\.

If all values in a set are true, the BOOL\_AND function returns `true` \(`t`\)\. If any value is false, the function returns `false` \(`f`\)\.

## Syntax<a name="r_BOOL_AND-synopsis"></a>

```
BOOL_AND ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BOOL_AND-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have a BOOLEAN or integer data type\. The return type of the function is BOOLEAN\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. For more information, see [DISTINCT support for bit\-wise aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.

## Examples<a name="r_bool_and_example"></a>

You can use the Boolean functions against either Boolean expressions or integer expressions\. For example, the following query return results from the standard USERS table in the TICKIT database, which has several Boolean columns\.

The BOOL\_AND function returns `false` for all five rows\. Not all users in each of those states likes sports\.

```
select state, bool_and(likesports) from users 
group by state order by state limit 5;

state | bool_and
------+---------
AB    | f
AK    | f
AL    | f
AZ    | f
BC    | f
(5 rows)
```