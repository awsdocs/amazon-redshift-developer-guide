# is\_scalar function<a name="r_is_scalar"></a>

Checks whether a variable is a scalar\. The is\_scalar function returns true for any value that is not an array or an object\. The function returns false for any other values, including null\.

The set of is\_array, is\_object, and is\_scalar cover all values except nulls\.

## Example<a name="r_is_scalar_example"></a>

The following query shows an is\_scalar function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (JSON_PARSE('{"name": "Joe"}'));

SELECT s, is_scalar(s.name) FROM t;

       s        | is_scalar
----------------+-----------
 {"name":"Joe"} | True
(1 row)
```