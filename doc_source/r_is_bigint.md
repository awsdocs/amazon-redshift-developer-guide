# is\_bigint function<a name="r_is_bigint"></a>

Checks whether a value is a bigint\. The is\_bigint function returns true for numbers of scale 0 in the 64\-bit range\. Otherwise, the function returns false for all other values, including null and floating point numbers\.

The is\_bigint function is a superset of is\_integer\.

## Example<a name="r_is_bigint_example"></a>

The following query shows an is\_bigint function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (5);

SELECT s, is_bigint(s) FROM t;

 s   is_bigint
---+-----------
 5  |   True
(1 row)
```