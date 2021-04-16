# is\_varchar function<a name="r_is_varchar"></a>

Checks whether a variable is a varchar\. The is\_varchar function returns true for all strings\. The function returns false for any other values\.

The is\_varchar function is a superset of the is\_char function\.

## Example<a name="r_is_varchar_example"></a>

The following query shows an is\_varchar function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES ('abc');

SELECT s, is_varchar(s) FROM t;

   s   | is_varchar
-------+------------
 "abc" | True
(1 row)
```