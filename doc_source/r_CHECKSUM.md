# CHECKSUM function<a name="r_CHECKSUM"></a>

Computes a checksum value for building a hash index\. 

## Syntax<a name="r_CHECKSUM-synopsis"></a>

```
CHECKSUM(expression)
```

## Argument<a name="r_CHECKSUM-argument"></a>

 *expression*   
The input expression must be a VARCHAR, INTEGER, or DECIMAL data type\. 

## Return type<a name="r_CHECKSUM-return-type"></a>

The CHECKSUM function returns an integer\. 

## Example<a name="r_CHECKSUM-example"></a>

The following example computes a checksum value for the COMMISSION column: 

```
select checksum(commission)
from sales
order by salesid
limit 10;

checksum
----------
10920
1140
5250
2625
2310
5910
11820
2955
8865
975
(10 rows)
```