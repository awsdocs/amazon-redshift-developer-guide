# is\_float function<a name="r_is_float"></a>

Checks whether a value is a floating point number\. The is\_float function returns true for floating point numbers \(float4 and float8\)\. The function returns false for any other values\.

The is\_decimal set and the is\_float set are disjoint\.

## Example<a name="r_is_float_example"></a>

The following query shows an is\_float function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES(2.22::FLOAT);

SELECT s, is_float(s) FROM t;

    s    | is_float
---------+----------
 2.22e+0 | True
(1 row)
```