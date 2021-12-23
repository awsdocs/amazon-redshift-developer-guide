# PIVOT and UNPIVOT examples<a name="r_FROM_clause-pivot-unpivot-examples"></a>

## PIVOT examples<a name="r_FROM_clause-pivot-examples"></a>

Set up the sample table and data and use them to run the subsequent example queries\.

```
CREATE TABLE part (
    partname varchar, 
    manufacturer varchar, 
    quality int, 
    price decimal(12, 2)
);

INSERT INTO part VALUES ('P1', 'M1', 2, 10.00);
INSERT INTO part VALUES ('P1', 'M2', NULL, 9.00);
INSERT INTO part VALUES ('P1', 'M3', 1, 12.00);

INSERT INTO part VALUES ('P2', 'M1', 1, 2.50);
INSERT INTO part VALUES ('P2', 'M2', 2, 3.75);
INSERT INTO part VALUES ('P2', 'M3', NULL, 1.90);

INSERT INTO part VALUES ('P3', 'M1', NULL, 7.50);
INSERT INTO part VALUES ('P3', 'M2', 1, 15.20);
INSERT INTO part VALUES ('P3', 'M3', NULL, 11.80);
```

PIVOT on `partname` with an `AVG` aggregation on `price`\.

```
SELECT *
FROM (SELECT partname, price FROM part) PIVOT (
    AVG(price) FOR partname IN ('P1', 'P2', 'P3')
);
```

The query results in the following output\.

```
  p1   |  p2  |  p3   
-------+------+-------
 10.33 | 2.71 | 11.50
```

The result of the preceding `PIVOT` example is equivalent to the following `GROUP BY` query\. However, the result rows are transformed into column names and columns\.

```
SELECT partname, avg(price)
FROM (SELECT partname, price FROM part)
WHERE partname IN ('P1', 'P2', 'P3')
GROUP BY partname;
```

The query results in the following output\.

```
 partname |  avg  
----------+-------
 P1       | 10.33
 P2       |  2.71
 P3       | 11.50
```

A `PIVOT` example with `manufacturer` as an implicit column\.

```
SELECT *
FROM (SELECT quality, manufacturer FROM part) PIVOT (
    count(*) FOR quality IN (1, 2, NULL)
);
```

The query results in the following output\.

```
 manufacturer | 1 | 2 | null 
--------------+---+---+------
 M1           | 1 | 1 |    1
 M2           | 1 | 1 |    1
 M3           | 1 | 0 |    2
```

 Input table columns that are not referenced in the `PIVOT` definition are added implicitly to the result table\. This is the case for the `manufacturer` column in the previous example\. The example also shows that `NULL` is a valid value for the `IN` operator\. 

`PIVOT` in the above example returns similar information as the following query, which includes `GROUP BY`\. The only difference is that `PIVOT` returns the value `0` for column `2` and the manufacturer `M3`\. The `GROUP BY` query does not contain a corresponding row\. In most cases `PIVOT` inserts `NULL` if a row does not have input data for a given column\. However, the count aggregate doesn't return `NULL` and `0` is used as the default value\.

```
SELECT manufacturer, quality, count(*)
FROM (SELECT quality, manufacturer FROM part)
WHERE quality IN (1, 2) OR quality IS NULL
GROUP BY manufacturer, quality;
```

The query results in the following output\.

```
 manufacturer | quality | count 
--------------+---------+-------
 M1           |       1 |     1
 M1           |       2 |     1
 M1           |         |     1
 M2           |       1 |     1
 M2           |       2 |     1
 M2           |         |     1
 M3           |       1 |     1
 M3           |         |     2
```

 The PIVOT operator accepts optional aliases on the aggregate expression and on each value for the `IN` operator\. Use aliases to customize the column names\. If there is no aggregate alias, only the `IN` list aliases are used\. Otherwise, the aggregate alias is appended to the column name with an underscore to separate the names\. 

```
SELECT *
FROM (SELECT quality, manufacturer FROM part) PIVOT (
    count(*) AS count FOR quality IN (1 AS high, 2 AS low, NULL AS na)
);
```

The query results in the following output\.

```
 manufacturer | high_count | low_count | na_count 
--------------+------------+-----------+----------
 M1           |          1 |         1 |        1
 M2           |          1 |         1 |        1
 M3           |          1 |         0 |        2
```

The following are usage notes for `PIVOT`\.
+ `PIVOT` can be applied to tables, sub\-queries, and common table expressions \(CTEs\)\. `PIVOT` cannot be applied to any `JOIN` expressions, recursive CTEs, `PIVOT`, or `UNPIVOT` expressions\. Also not supported are `SUPER` unnested expressions and Redshift Spectrum nested tables\.
+  `PIVOT` currently supports the `COUNT`, `SUM`, `MIN`, `MAX`, and `AVG` aggregate functions\. 
+ The `PIVOT` aggregate expression has to be a call of a supported aggregate function\. Complex expressions on top of the aggregate are not supported\. The aggregate arguments cannot contain references to tables other than the `PIVOT` input table\. Correlated references to a parent query are also not supported\. The aggregate argument may contain sub\-queries which can be correlated internally or on the `PIVOT` input table\.
+  The `PIVOT IN` list values cannot be column references or sub\-queries\. Each value must be type compatible with the `FOR` column reference\. 
+  If the `IN` list values do not have aliases, `PIVOT` will generate default column names\. For constant `IN` values such as ‘abc’ or 5 the default column name is the constant itself\. For any complex expression, the column name is a standard Redshift default name such as `?column?`\. 

## UNPIVOT examples<a name="r_FROM_clause-unpivot-examples"></a>

Set up the sample data and use it to run the subsequent examples\.

```
CREATE TABLE count_by_color (quality varchar, red int, green int, blue int);

INSERT INTO count_by_color VALUES ('high', 15, 20, 7);
INSERT INTO count_by_color VALUES ('normal', 35, NULL, 40);
INSERT INTO count_by_color VALUES ('low', 10, 23, NULL);
```

`UNPIVOT` on input columns red, green, and blue\.

```
SELECT * 
FROM (SELECT red, green, blue FROM count_by_color) UNPIVOT (
    cnt FOR color IN (red, green, blue)
);
```

The query results in the following output\.

```
 color | cnt 
-------+-----
 red   |  15
 red   |  35
 red   |  10
 green |  20
 green |  23
 blue  |   7
 blue  |  40
```

By default, `NULL` values in the input column are skipped and do not yield a result row\. 

The following example shows `UNPIVOT` with `INCLUDE NULLS`\.

```
SELECT * 
FROM (
    SELECT red, green, blue 
    FROM count_by_color
) UNPIVOT INCLUDE NULLS (
    cnt FOR color IN (red, green, blue)
);
```

The following is the resulting output\.

```
 color | cnt 
-------+-----
 red   |  15
 red   |  35
 red   |  10
 green |  20
 green |    
 green |  23
 blue  |   7
 blue  |  40
 blue  |
```

If the `INCLUDING NULLS` parameter is set, `NULL` input values generate result rows\.

`The following query shows UNPIVOT` with `quality` as an implicit column\.

```
SELECT * 
FROM count_by_color UNPIVOT (
    cnt FOR color IN (red, green, blue)
);
```

The query results in the following output\.

```
 quality | color | cnt 
---------+-------+-----
 high    | red   |  15
 normal  | red   |  35
 low     | red   |  10
 high    | green |  20
 low     | green |  23
 high    | blue  |   7
 normal  | blue  |  40
```

Columns of the input table that are not referenced in the `UNPIVOT` definition are added implicitly to the result table\. In the example, this is the case for the `quality` column\.

The following example shows `UNPIVOT` with aliases for values in the `IN` list\.

```
SELECT * 
FROM count_by_color UNPIVOT (
    cnt FOR color IN (red AS r, green AS g, blue AS b)
);
```

The previous query results in the following output\.

```
 quality | color | cnt 
---------+-------+-----
 high    | r     |  15
 normal  | r     |  35
 low     | r     |  10
 high    | g     |  20
 low     | g     |  23
 high    | b     |   7
 normal  | b     |  40
```

The `UNPIVOT` operator accepts optional aliases on each `IN` list value\. Each alias provides customization of the data in each `value` column\.

The following are usage notes for `UNPIVOT`\.
+ `UNPIVOT` can be applied to tables, sub\-queries, and common table expressions \(CTEs\)\. `UNPIVOT` cannot be applied to any `JOIN` expressions, recursive CTEs, `PIVOT`, or `UNPIVOT` expressions\. Also not supported are `SUPER` unnested expressions and Redshift Spectrum nested tables\.
+ The `UNPIVOT IN` list must contain only input table column references\. The `IN` list columns must have a common type that they are all compatible with\. The `UNPIVOT` value column has this common type\. The `UNPIVOT` name column is of type `VARCHAR`\.
+ If an `IN` list value does not have an alias, `UNPIVOT` uses the column name as a default value\.