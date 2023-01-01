# Querying semistructured data<a name="query-super"></a>

Amazon Redshift uses the PartiQL language to offer SQL\-compatible access to relational, semistructured, and nested data\. 

PartiQL operates with dynamic types\. This approach enables intuitive filtering, joining, and aggregation on the combination of structured, semistructured, and nested datasets\. The PartiQL syntax uses dotted notation and array subscript for path navigation when accessing nested data\. It also enables the FROM clause items to iterate over arrays and use for unnest operations\. Following, you can find descriptions of the different query patterns that combine the use of the SUPER data type with path and array navigation, unnesting, unpivoting, and joins\. 

## Navigation<a name="navigation"></a>

Amazon Redshift uses PartiQL to enable navigation into arrays and structures using the \[\.\.\.\] bracket and dot notation respectively\. Furthermore, you can mix navigation into structures using the dot notation and arrays using the bracket notation\. For example, the following example assumes that the `c_orders` SUPER data column is an array with a structure and an attribute is named `o_orderkey`\.

To ingest data in the `customer_orders_lineitem` table, run the following command\. Replace the IAM role with your own credentials\.

```
COPY customer_orders_lineitem FROM 's3://redshift-downloads/semistructured/tpch-nested/data/json/customer_orders_lineitem'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT JSON 'auto';

SELECT c_orders[0].o_orderkey FROM customer_orders_lineitem;
```

Amazon Redshift also uses a table alias as a prefix to the notation\. The following example is the same query as the previous example\.

```
SELECT cust.c_orders[0].o_orderkey FROM customer_orders_lineitem AS cust;
```

You can use the dot and bracket notations in all types of queries, such as filtering, join, and aggregation\. You can use these notations in a query in which there are normally column references\. The following example uses a SELECT statement that filters results\.

```
SELECT count(*) FROM customer_orders_lineitem WHERE c_orders[0].o_orderkey IS NOT NULL;
```

The following example uses the bracket and dot navigation in both GROUP BY and ORDER BY clauses\.

```
SELECT c_orders[0].o_orderdate,
       c_orders[0].o_orderstatus,
       count(*)
FROM customer_orders_lineitem
WHERE c_orders[0].o_orderkey IS NOT NULL
GROUP BY c_orders[0].o_orderstatus,
         c_orders[0].o_orderdate
ORDER BY c_orders[0].o_orderdate;
```

## Unnesting queries<a name="unnest"></a>

To unnest queries, Amazon Redshift uses the PartiQL syntax to iterate over SUPER arrays\. It does this by navigating the array using the FROM clause of a query\. Using the previous example, the following example iterates over the attribute values for `c_orders`\.

```
SELECT c.*, o FROM customer_orders_lineitem c, c.c_orders o;
```

The unnesting syntax is an extension of the FROM clause\. In standard SQL, the FROM clause `x (AS) y` means that `y` iterates over each tuple in relation `x`\. In this case, `x` refers to a relation and `y` refers to an alias for relation `x`\. Similarly, the PartiQL syntax of unnesting using the FROM clause item `x (AS) y` means that `y` iterates over each \(SUPER\) value in \(SUPER\) array expression x\. In this case, `x` is a SUPER expression and `y` is an alias for `x`\.

The left operand can also use the dot and bracket notation for regular navigation\. In the previous example, `customer_orders_lineitem c` is the iteration over the `customer_order_lineitem` base table and `c.c_orders o` is the iteration over the `c.c_orders` array\. To iterate over the `o_lineitems` attribute, which is an array within an array, you add multiple clauses\.

```
SELECT c.*, o, l FROM customer_orders_lineitem c, c.c_orders o, o.o_lineitems l;
```

Amazon Redshift also supports an array index when iterating over the array using the AT keyword\. The clause `x AS y AT z` iterates over array `x` and generates the field `z,` which is the array index\. The following example shows how an array index works\.

```
SELECT c_name,
       orders.o_orderkey AS orderkey,
       index AS orderkey_index
FROM customer_orders_lineitem c, c.c_orders AS orders AT index 
ORDER BY orderkey_index;

c_name             | orderkey | orderkey_index
-------------------+----------+----------------
Customer#000008251 | 3020007  |        0
Customer#000009452 | 4043971  |        0
  (2 rows)
```

The following example iterates over a scalar array\.

```
CREATE TABLE bar AS SELECT json_parse('{"scalar_array": [1, 2.3, 45000000]}') AS data;

SELECT index, element FROM bar AS b, b.data.scalar_array AS element AT index;

 index | element
-------+----------
     0 | 1
     1 | 2.3
     2 | 45000000
(3 rows)
```

The following example iterates over an array of multiple levels\. The example uses multiple unnest clauses to iterate into the innermost arrays\. The `f.multi_level_array` AS array iterates over `multi_level_array`\. The array AS element is the iteration over the arrays within `multi_level_array`\.

```
CREATE TABLE foo AS SELECT json_parse('[[1.1, 1.2], [2.1, 2.2], [3.1, 3.2]]') AS multi_level_array;

SELECT array, element FROM foo AS f, f.multi_level_array AS array, array AS element;

   array   | element
-----------+---------
 [1.1,1.2] | 1.1
 [1.1,1.2] | 1.2
 [2.1,2.2] | 2.1
 [2.1,2.2] | 2.2
 [3.1,3.2] | 3.1
 [3.1,3.2] | 3.2
(6 rows)
```

For more information about the FROM clause, see [FROM clause](r_FROM_clause30.md)\.

## Object unpivoting<a name="unpivoting"></a>

To perform object unpivoting, Amazon Redshift uses the PartiQL syntax to iterate over SUPER objects\. It does this using the FROM clause of a query with the UNPIVOT keyword\. The following query iterates over the `c.c_orders[0]` object\.

```
SELECT attr as attribute_name, json_typeof(val) as value_type 
FROM customer_orders_lineitem c, UNPIVOT c.c_orders[0] AS val AT attr 
WHERE c_custkey = 9451;

 attribute_name  | value_type
-----------------+------------
 o_orderstatus   | string
 o_clerk         | string
 o_lineitems     | array
 o_orderdate     | string
 o_shippriority  | number
 o_totalprice    | number
 o_orderkey      | number
 o_comment       | string
 o_orderpriority | string
(9 rows)
```

As with unnesting, the unpivoting syntax is also an extension of the FROM clause\. The difference is that the unpivoting syntax uses the UNPIVOT keyword to indicate that it's iterating over an object instead of an array\. It uses the AS `value_alias` for iteration over all the values inside an object and uses the AT `attribute_alias` for iterating over all the attributes\.

Amazon Redshift also supports using object unpivoting and array unnesting in a single FROM clause as follows\.

```
SELECT attr as attribute_name, val as object_value
FROM customer_orders_lineitem c, c.c_orders AS o, UNPIVOT o AS val AT attr 
WHERE c_custkey = 9451;
```

When you use object unpivoting, Amazon Redshift doesn't support correlated unpivoting\. Specifically, suppose that you have a case where there are multiple examples of unpivoting in different query levels and the inner unpivoting references the outer one\. Amazon Redshift doesn't support this type of multiple unpivoting\.

For more information about the FROM clause, see [FROM clause](r_FROM_clause30.md)\. For examples that show how to query structured data, with PIVOT and UNPIVOT, see [PIVOT and UNPIVOT examples](r_FROM_clause-pivot-unpivot-examples.md)\.

## Dynamic typing<a name="dynamic-typing-lax-processing"></a>

Dynamic typing doesn't require explicit casting of data that is extracted from the dot and bracket paths\. Amazon Redshift uses dynamic typing to process schemaless SUPER data without the need to declare the data types before you use them in your query\. Dynamic typing uses the results of navigating into SUPER data columns without having to explicitly cast them into Amazon Redshift types\. Dynamic typing is most useful in joins and GROUP BY clauses\. The following example uses a SELECT statement that requires no explicit casting of the dot and bracket expressions to the usual Amazon Redshift types\. For information about type compatibility and conversion, see [Type compatibility and conversion](c_Supported_data_types.md#r_Type_conversion)\.

```
SELECT c_orders[0].o_orderkey
FROM customer_orders_lineitem
WHERE c_orders[0].o_orderstatus = 'P';
```

The equality sign in this query evaluates to `true` when c\_orders\[0\]\.o\_orderstatus is the string ‘P’\. In all other cases, the equality sign evaluates to `false`, including the cases where the arguments of the equality are different types\.

### Dynamic and static typing<a name="dynamic-typing-lax-processing-dynamic-and-static"></a>

Without using dynamic typing, you can't determine whether c\_orders\[0\]\.o\_orderstatus is a string, an integer, or a structure\. You can only determine that c\_orders\[0\]\.o\_orderstatus is a SUPER data type, which can be an Amazon Redshift scalar, an array, or a structure\. The static type of c\_orders\[0\]\.o\_orderstatus is a SUPER data type\. Conventionally, a type is implicitly a static type in SQL\.

Amazon Redshift uses dynamic typing to the processing of schemaless data\. When the query evaluates the data, c\_orders\[0\]\.o\_orderstatus turns out to be a specific type\. For example, evaluating c\_orders\[0\]\.o\_orderstatus on the first record of customer\_orders\_lineitem may result into an integer\. Evaluating on the second record may result into a string\. These are the dynamic types of the expression\.

When using an SQL operator or function with dot and bracket expressions that have dynamic types, Amazon Redshift produces results similar to using standard SQL operator or function with the respective static types\. In this example, when the dynamic type of the path expression is a string, the comparison with the string ‘P’ is meaningful\. Whenever the dynamic type of c\_orders\[0\]\.o\_orderstatus is any other data type except being a string, the equality returns false\. Other functions return null when mistyped arguments are used\.

The following example writes the previous query with static typing:

```
SELECT c_custkey
FROM customer_orders_lineitem
WHERE CASE WHEN JSON_TYPEOF(c_orders[0].o_orderstatus) = 'string'
           THEN c_orders[0].o_orderstatus::VARCHAR = 'P'
           ELSE FALSE END;
```

Note the following distinction between equality predicates and comparison predicates\. In the previous example, if you replace the equality predicate with a less\-than\-or\-equal predicate, the semantics produce null instead of false\.

```
SELECT c_orders[0]. o_orderkey
FROM customer_orders_lineitem
WHERE c_orders[0].o_orderstatus <= 'P';
```

In this example, if c\_orders\[0\]\.o\_orderstatus is a string, Amazon Redshift returns true if it is alphabetically equal to or smaller than ‘P’\. Amazon Redshift returns false if it is alphabetically larger than 'P'\. However, if c\_orders\[0\]\.o\_orderstatus is not a string, Amazon Redshift returns null since Amazon Redshift can't compare values of different types, as shown in the following query:

```
SELECT c_custkey
FROM customer_orders_lineitem
WHERE CASE WHEN JSON_TYPEOF(c_orders[0].o_orderstatus) = 'string'
           THEN c_orders[0].o_orderstatus::VARCHAR <= 'P'
           ELSE NULL END;
```

Dynamic typing doesn't exclude from comparisons of types that are minimally comparable\. For example, you can convert both CHAR and VARCHAR Amazon Redshift scalar types to SUPER\. They are comparable as strings, including ignoring trailing white\-space characters similar to Amazon Redshift CHAR and VARCHAR types\. Similarly, integers, decimals, and floating\-point values are comparable as SUPER values\. Specifically for decimal columns, each value can also have a different scale\. Amazon Redshift still considers them as dynamic types\.

Amazon Redshift also supports equality on objects and arrays that are evaluated as deep equal, such as evaluating deep into objects or arrays and comparing all attributes\. Use deep equal with caution, because the process of performing deep equal can be time\-consuming\.

### Using dynamic typing for joins<a name="dynamic-typing-lax-processing-joins"></a>

For joins, dynamic typing automatically matches values with different dynamic types without performing a long CASE WHEN analysis to find out what data types may appear\. For example, assume that your organization changed the format that it was using for part keys over time\.

The initial integer part keys issued are replaced by string part keys, such as ‘A55’, and later replaced again by array part keys, such as \[‘X’, 10\] combining a string and a number\. Amazon Redshift doesn't have to perform a lengthy case analysis about part keys and can use joins as shown in the following example\.

```
SELECT c.c_name
    ,l.l_extendedprice
    ,l.l_discount
FROM customer_orders_lineitem c
    ,c.c_orders o
    ,o.o_lineitems l
    ,supplier_partsupp s
    ,s.s_partsupps ps
WHERE l.l_partkey = ps.ps_partkey
AND c.c_nationkey = s.s_nationkey
ORDER BY c.c_name;
```

The following example shows how complex and inefficient the same query can be without using dynamic typing:

```
SELECT c.c_name
    ,l.l_extendedprice
    ,l.l_discount
FROM customer_orders_lineitem c
    ,c.c_orders o
    ,o.o_lineitems l
    ,supplier_partsupp s
    ,s.s_partsupps ps
WHERE CASE WHEN IS_INTEGER(l.l_partkey) AND IS_INTEGER(ps.ps_partkey)
           THEN l.l_partkey::integer = ps.ps_partkey::integer
           WHEN IS_VARCHAR(l.l_partkey) AND IS_VARCHAR(ps.ps_partkey)
           THEN l.l_partkey::varchar = ps.ps_partkey::varchar
           WHEN IS_ARRAY(l.l_partkey) AND IS_ARRAY(ps.ps_partkey)
                AND IS_VARCHAR(l.l_partkey[0]) AND IS_VARCHAR(ps.ps_partkey[0])
                AND IS_INTEGER(l.l_partkey[1]) AND IS_INTEGER(ps.ps_partkey[1])
           THEN l.l_partkey[0]::varchar = ps.ps_partkey[0]::varchar
                AND l.l_partkey[1]::integer = ps.ps_partkey[1]::integer
           ELSE FALSE END
AND c.c_nationkey = s.s_nationkey
ORDER BY c.c_name;
```

## Lax semantics<a name="lax-semantics"></a>

By default, navigation operations on SUPER values return null instead of returning an error out when the navigation is invalid\. Object navigation is invalid if the SUPER value is not an object or if the SUPER value is an object but doesn't contain the attribute name used in the query\. For example, the following query accesses an invalid attribute name in the SUPER data column cdata:

```
SELECT c.c_orders.something FROM customer_orders_lineitem c;
```

Array navigation returns null if the SUPER value is not an array or the array index is out of bounds\. The following query returns null because c\_orders\[1\]\[1\] is out of bounds\. 

```
SELECT c.c_orders[1][1] FROM customer_orders_lineitem c;
```

Lax semantics is especially useful when using dynamic typing to cast a SUPER value\. Casting a SUPER value to the wrong type returns null instead of an error if the cast is invalid\. For example, the following query returns null because it can't cast the string value 'Good' of the object attribute o\_orderstatus to INTEGER\. Amazon Redshift returns an error for a VARCHAR to INTEGER cast but not for a SUPER cast\.

```
SELECT c.c_orders.o_orderstatus::integer FROM customer_orders_lineitem c;
```

## Types of introspection<a name="introspection"></a>

SUPER data columns support inspection functions that return the dynamic type and other type information about the SUPER value\. The most common example is the JSON\_TYPEOF scalar function that returns a VARCHAR with values boolean, number, string, object, array, or null, depending on the dynamic type of the SUPER value\. Amazon Redshift supports the following boolean functions for SUPER data columns:
+ DECIMAL\_PRECISION
+ DECIMAL\_SCALE
+ IS\_ARRAY
+ IS\_BIGINT
+ IS\_CHAR
+ IS\_DECIMAL
+ IS\_FLOAT
+ IS\_INTEGER
+ IS\_OBJECT
+ IS\_SCALAR
+ IS\_SMALLINT
+ IS\_VARCHAR
+ JSON\_TYPEOF

All these functions return false if the input value is null\. IS\_SCALAR, IS\_OBJECT, and IS\_ARRAY are mutually exclusive and cover all possible values except for null\.

To infer the types corresponding to the data, Amazon Redshift uses the JSON\_TYPEOF function that returns the type of \(the top level of\) the SUPER value as shown in the following example:

```
SELECT JSON_TYPEOF(r_nations) FROM region_nations;
 json_typeof
 -------------
 array
(1 row)
```

```
SELECT JSON_TYPEOF(r_nations[0].n_nationkey) FROM region_nations;
 json_typeof
 -------------
 number
```

Amazon Redshift sees this as a single long string, similar to inserting this value into a VARCHAR column instead of a SUPER\. Since the column is SUPER, the single string is still a valid SUPER value and the difference is noted in JSON\_TYPEOF:

```
SELECT IS_VARCHAR(r_nations[0].n_name) FROM region_nations;
 is_varchar
-------------
 true
(1 row)
```

```
SELECT r_nations[4].n_name FROM region_nations
WHERE CASE WHEN IS_INTEGER(r_nations[4].n_nationkey) 
           THEN r_nations[4].n_nationkey::INTEGER = 15 
           ELSE false END;
```

## Order by<a name="order-by"></a>

Amazon Redshift doesn't define SUPER comparisons among values with different dynamic types\. A SUPER value that is a string is neither smaller nor larger than a SUPER value that is a number\. To use ORDER BY clauses with SUPER columns, Amazon Redshift defines a total ordering among different types to be observed when Amazon Redshift ranks SUPER values using ORDER BY clauses\. The order among dynamic types is boolean, number, string, array, object\. The following example shows the orders of different types:

```
INSERT INTO region_nations VALUES 
(100,'name1','comment1','AWS'), 
(200,'name2','comment2',1),
(300,'name3','comment3',ARRAY(1, 'abc', null)),
(400,'name4','comment4',-2.5),
(500,'name5','comment5','Amazon');

SELECT r_nations FROM region_nations order by r_nations;

r_nations
----------------
 -2.5
 1
 "Amazon"
 "AWS"
 [1,"abc",null]
(5 rows)
```

For more information about the ORDER BY clause, see [ORDER BY clause](r_ORDER_BY_clause.md)\.
