# error\_on\_nondeterministic\_update<a name="r_error_on_nondeterministic_update"></a>

## Values \(default in bold\)<a name="r_error_on_nondeterministic_update-values"></a>

**false**, true

## Description<a name="description"></a>

Specifies whether UPDATE queries with multiple matches per row throws an error\.

## Example<a name="example"></a>

```
SSET error_on_nondeterministic_update TO true;
            
CREATE TABLE t1(x1 int, y1 int);

CREATE TABLE t2(x2 int, y2 int);

INSERT INTO t1 VALUES (1,10), (2,20), (3,30);

INSERT INTO t2 VALUES (2,40), (2,50);

UPDATE t1 SET y1=y2 FROM t2 WHERE x1=x2; 

ERROR: Found multiple matches to update the same tuple.
```