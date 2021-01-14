# ANY\_VALUE function<a name="r_ANY_VALUE"></a>

The ANY\_VALUE function returns any value from the input expression values nondeterministically\. This function can return NULL if the input expression doesn't result in any rows being returned or contains a NULL value\.

## Syntax<a name="r_ANY_VALUE-synopsis"></a>

```
ANY_VALUE ( [ DISTINCT | ALL ] expression )
```

## Arguments<a name="r_ANY_VALUE-arguments"></a>

DISTINCT \| ALL  
Specify either DISTINCT or ALL to return any value from the input expression values\. The DISTINCT argument has no effect and is ignored\.

 *expression *   
The target column or expression on which the function operates\.

## Returns<a name="r_ANY_VALUE-returns"></a>

A value from the input expression values\. The following data types are returned:
+ SMALLINT
+ INTEGER
+ BIGINT
+ DECIMAL
+ REAL
+ DOUBLE
+ BOOLEAN
+ CHAR
+ VARCHAR
+ DATE
+ TIMESTAMP
+ TIMESTAMPTZ
+ TIME
+ TIMETZ

## Usage notes<a name="r_ANY_VALUE-usage-notes"></a>

The geometry and HLL data types are not supported\. 

## Examples<a name="r_ANY_VALUE-examples"></a>

The following example returns an instance of any employee name from each department\. 

```
select any_value(name) as name, department_id from employee group by department_id;
```

Following are the results\.

```
 name  | department_id
-------+---------------
 Smith |           106
 Jack  |           100
 Mark  |           102
(3 rows)
```