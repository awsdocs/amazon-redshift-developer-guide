# SIZE<a name="r_SIZE"></a>

 Returns the binary in\-memory size of a SUPER type constant or expression as an integer\. 

## Syntax<a name="r_SIZE-synopsis"></a>

```
SIZE(super_expression)
```

## Parameters<a name="r_SIZE-parameters"></a>

*super\_expression*  
 A SUPER type constant or expression\. 

## Returns<a name="r_SIZE-returns"></a>

 Integer 

## Example<a name="r_SIZE-examples"></a>

 The following example uses SIZE to get the in\-memory size of several SUPER type expressions\. 

 First, create and populate a table\. 

```
CREATE TABLE test_super_size(a super);
            
INSERT INTO test_super_size 
VALUES
( 
  (null),
  (TRUE),
  (json_parse('[0,1,2,3]')),
  (json_parse('{"a":0,"b":1,"c":2,"d":3}')
);
```

 Running SIZE returns the binary size of SUPER type expressions as an integer\. 

```
SELECT a, SIZE(a) 
FROM test_super_size 
ORDER BY 2, 1;

             a             | size
---------------------------+------
 true                      |    4
                           |    4
 [0,1,2,3]                 |   23
 {"a":0,"b":1,"c":2,"d":3} |   52
(4 rows)
```