# Function expressions<a name="r_function_expressions"></a>

## Syntax<a name="r_function_expressions-syntax"></a>

Any built\-in can be used as an expression\. The syntax for a function call is the name of a function followed by its argument list in parentheses\. 

```
function ( [expression [, expression...]] )
```

## Arguments<a name="r_function_expressions-arguments"></a>

 *function*   
Any built\-in function\.

 *expression*   
Any expression\(s\) matching the data type and parameter count expected by the function\. 

## Examples<a name="r_function_expressions-examples"></a>

```
abs (variable)
select avg (qtysold + 3) from sales;
select dateadd (day,30,caldate) as plus30days from date;
```