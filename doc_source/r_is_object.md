# is\_object function<a name="r_is_object"></a>

Checks whether a variable is an object\. The is\_object function returns true for objects, including empty objects\. The function returns false for any other values, including null\.

## Example<a name="r_is_object_example"></a>

The following query shows an is\_object function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (JSON_PARSE('{"name": "Joe"}'));

SELECT s, is_object(s) FROM t;

       s        | is_object
----------------+-----------
 {"name":"Joe"} | True
(1 row)
```