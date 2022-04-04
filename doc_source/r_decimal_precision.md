# decimal\_precision function<a name="r_decimal_precision"></a>

Checks the precision of the maximum total number of decimal digits to be stored\. This number includes both the left and right digits of the decimal point\. The range of the precision is from 1 to 38, with a default of 38\.

## Syntax<a name="r_decimal_precision-synopsis"></a>

```
decimal_precision (super_expression)
```

## Arguments<a name="r_decimal_precision-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_decimal_precision-returns"></a>

Integer

## Example<a name="r_decimal_precision_example"></a>

The following query applies the decimal\_precision function to the table t\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (3.14159);

SELECT decimal_precision(s) FROM t;

 decimal_precision
------------------------
              6
(1 row)
```