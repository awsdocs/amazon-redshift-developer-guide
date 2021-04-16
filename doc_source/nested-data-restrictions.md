# Nested data limitations<a name="nested-data-restrictions"></a>

The following limitations apply to nested data:
+ An array can only contain scalars or `struct` types\. `Array` types can't contain `array` or `map` types\.
+ Redshift Spectrum supports complex data types only as external tables\.
+ Query and subquery result columns must be scalar\. 
+ If an `OUTER JOIN` expression refers to a nested table, it can refer only to that table and its nested arrays \(and maps\)\. If an `OUTER JOIN` expression doesn't refer to a nested table, it can refer to any number of non\-nested tables\.
+ If a `FROM` clause in a subquery refers to a nested table, it can't refer to any other table\.
+ If a subquery depends on a nested table that refers to a parent, you can use the parent only in the `FROM` clause\. You can't use the query in any other clauses, such as a `SELECT` or `WHERE` clause\. For example, the following query isn't run\. 

  ```
  SELECT c.name.given 
  FROM   spectrum.customers c 
  WHERE (SELECT COUNT(c.id) FROM c.phones p WHERE p LIKE '858%') > 1
  ```

  The following query works because the parent `c` is used only in the `FROM` clause of the subquery\.

  ```
  SELECT c.name.given 
  FROM   spectrum.customers c 
  WHERE (SELECT COUNT(*) FROM c.phones p WHERE p LIKE '858%') > 1
  ```
+ A subquery that accesses nested data anywhere other than the `FROM` clause must return a single value\. The only exceptions are `(NOT) EXISTS` operators in a `WHERE` clause\.
+ `(NOT) IN` is not supported\.
+ The maximum nesting depth for all nested types is 100\. This restriction applies to all file formats \(Parquet, ORC, Ion, and JSON\)\.
+ Aggregation subqueries that access nested data can only refer to `arrays` and `maps` in their `FROM` clause, not to an external table\. 