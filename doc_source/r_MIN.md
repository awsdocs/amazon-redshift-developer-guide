# MIN function<a name="r_MIN"></a>

 The MIN function returns the minimum value in a set of rows\. DISTINCT or ALL may be used but do not affect the result\.

## Syntax<a name="r_MIN-synopsis"></a>

```
MIN ( [ DISTINCT | ALL ] expression )
```

## Arguments<a name="r_MIN-arguments"></a>

 *expression *   
The target column or expression that the function operates on\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values from the specified expression before calculating the minimum\. With the argument ALL, the function retains all duplicate values from the expression for calculating the minimum\. ALL is the default\.

## Data types<a name="c_Supported_data_types_min"></a>

Accepts any data type except Boolean as input\. Returns the same data type as *expression*\. The Boolean equivalent of the MIN function is [BOOL\_AND function](r_BOOL_AND.md), and the Boolean equivalent of MAX is [BOOL\_OR function](r_BOOL_OR.md)\. 

## Examples<a name="r_MIN-examples"></a>

Find the lowest price paid from all sales:

```
select min(pricepaid) from sales;

min
-------
20.00
(1 row)
```

Find the lowest price paid per ticket from all sales:

```
select min(pricepaid/qtysold)as min_ticket_price
from sales;

min_ticket_price
------------------
20.00000000
(1 row)
```