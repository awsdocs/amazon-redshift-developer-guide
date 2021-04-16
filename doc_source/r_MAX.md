# MAX function<a name="r_MAX"></a>

 The MAX function returns the maximum value in a set of rows\. DISTINCT or ALL may be used but do not affect the result\. 

## Syntax<a name="r_MAX-synopsis"></a>

```
MAX ( [ DISTINCT | ALL ] expression )
```

## Arguments<a name="r_MAX-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

DISTINCT \| ALL   
With the argument DISTINCT, the function eliminates all duplicate values from the specified expression before calculating the maximum\. With the argument ALL, the function retains all duplicate values from the expression for calculating the maximum\. ALL is the default\. 

## Data types<a name="c_Supported_data_types_max"></a>

Accepts any data type except Boolean as input\. Returns the same data type as *expression*\. The Boolean equivalent of the MIN function is the [BOOL\_AND function](r_BOOL_AND.md), and the Boolean equivalent of MAX is the [BOOL\_OR function](r_BOOL_OR.md)\. 

## Examples<a name="r_MAX-examples"></a>

Find the highest price paid from all sales: 

```
select max(pricepaid) from sales;

max
----------
12624.00
(1 row)
```

Find the highest price paid per ticket from all sales: 

```
select max(pricepaid/qtysold) as max_ticket_price
from sales;

max_ticket_price
-----------------
2500.00000000
(1 row)
```