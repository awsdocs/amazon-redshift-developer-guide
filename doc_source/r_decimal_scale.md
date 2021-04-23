# decimal\_scale function<a name="r_decimal_scale"></a>

Checks the number of decimal digits to be stored to the right of the decimal point\. The range of the scale is from 0 to the precision point, with a default of 0\.

## Example<a name="r_decimal_scale_example"></a>

The following query applies the decimal\_scale function to the table t\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (3.14159);

SELECT decimal_scale(s) FROM t;
 decimal_scale
-----------------------
              5
(1 row)
```