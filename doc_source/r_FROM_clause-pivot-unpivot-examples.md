# PIVOT and UNPIVOT examples<a name="r_FROM_clause-pivot-unpivot-examples"></a>

PIVOT and UNPIVOT are parameters in the FROM clause that rotate query output from rows to columns and columns to rows, respectively\. They represent tabular query results in a format that's easy to read\. The following examples use test data and queries to show how to use them\.

For more information about these and other parameters, see [FROM clause](https://docs.aws.amazon.com/redshift/latest/dg/r_FROM_clause30.html)\.

## PIVOT examples<a name="r_FROM_clause-pivot-examples"></a>

Set up the sample table and data and use them to run the subsequent example queries\.

```
CREATE TABLE part (
    partname varchar,
    manufacturer varchar,
    quality int,
    price decimal(12, 2)
);

INSERT INTO part VALUES ('prop', 'local parts co', 2, 10.00);
INSERT INTO part VALUES ('prop', 'big parts co', NULL, 9.00);
INSERT INTO part VALUES ('prop', 'small parts co', 1, 12.00);

INSERT INTO part VALUES ('rudder', 'local parts co', 1, 2.50);
INSERT INTO part VALUES ('rudder', 'big parts co', 2, 3.75);
INSERT INTO part VALUES ('rudder', 'small parts co', NULL, 1.90);

INSERT INTO part VALUES ('wing', 'local parts co', NULL, 7.50);
INSERT INTO part VALUES ('wing', 'big parts co', 1, 15.20);
INSERT INTO part VALUES ('wing', 'small parts co', NULL, 11.80);
```

PIVOT on `partname` with an `AVG` aggregation on `price`\.

```
SELECT *
FROM (SELECT partname, price FROM part) PIVOT (
    AVG(price) FOR partname IN ('prop', 'rudder', 'wing')
);
```

The query results in the following output\.

```
  prop   |  rudder  |  wing
---------+----------+---------
 10.33   | 2.71     | 11.50
```

In the previous example, the results are transformed into columns\. The following example shows a `GROUP BY` query that returns the average prices in rows, rather than in columns\.

```
SELECT partname, avg(price)
FROM (SELECT partname, price FROM part)
WHERE partname IN ('prop', 'rudder', 'wing')
GROUP BY partname;
```

The query results in the following output\.

```
 partname |  avg
----------+-------
 prop     | 10.33
 rudder   |  2.71
 wing     | 11.50
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
 manufacturer      | 1  | 2  | null
-------------------+----+----+------
 local parts co    | 1  | 1  |  1
 big parts co      | 1  | 1  |  1
 small parts co    | 1  | 0  |  2
```

 Input table columns that are not referenced in the `PIVOT` definition are added implicitly to the result table\. This is the case for the `manufacturer` column in the previous example\. The example also shows that `NULL` is a valid value for the `IN` operator\. 

`PIVOT` in the above example returns similar information as the following query, which includes `GROUP BY`\. The difference is that `PIVOT` returns the value `0` for column `2` and the manufacturer `small parts co`\. The `GROUP BY` query does not contain a corresponding row\. In most cases, `PIVOT` inserts `NULL` if a row doesn't have input data for a given column\. However, the count aggregate doesn't return `NULL` and `0` is the default value\.

```
SELECT manufacturer, quality, count(*)
FROM (SELECT quality, manufacturer FROM part)
WHERE quality IN (1, 2) OR quality IS NULL
GROUP BY manufacturer, quality
ORDER BY manufacturer;
```

The query results in the following output\.

```
 manufacturer        | quality | count
---------------------+---------+-------
 big parts co        |         |     1
 big parts co        |       2 |     1
 big parts co        |       1 |     1
 local parts co      |       2 |     1
 local parts co      |       1 |     1
 local parts co      |         |     1
 small parts co      |       1 |     1
 small parts co      |         |     2
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
 manufacturer      | high_count  | low_count | na_count
-------------------+-------------+-----------+----------
 local parts co    |           1 |         1 |        1
 big parts co      |           1 |         1 |        1
 small parts co    |           1 |         0 |        2
```

Set up the following sample table and data and use them to run the subsequent example queries\. The data represents booking dates for a collection of hotels\.

```
CREATE TABLE bookings (
    booking_id int,
    hotel_code char(8),
    booking_date date,
    price decimal(12, 2)
);

INSERT INTO bookings VALUES (1, 'FOREST_L', '02/01/2023', 75.12);
INSERT INTO bookings VALUES (2, 'FOREST_L', '02/02/2023', 75.00);
INSERT INTO bookings VALUES (3, 'FOREST_L', '02/04/2023', 85.54);

INSERT INTO bookings VALUES (4, 'FOREST_L', '02/08/2023', 75.00);
INSERT INTO bookings VALUES (5, 'FOREST_L', '02/11/2023', 75.00);
INSERT INTO bookings VALUES (6, 'FOREST_L', '02/14/2023', 90.00);

INSERT INTO bookings VALUES (7, 'FOREST_L', '02/21/2023', 60.00);
INSERT INTO bookings VALUES (8, 'FOREST_L', '02/22/2023', 85.00);
INSERT INTO bookings VALUES (9, 'FOREST_L', '02/27/2023', 90.00);

INSERT INTO bookings VALUES (10, 'DESERT_S', '02/01/2023', 98.00);
INSERT INTO bookings VALUES (11, 'DESERT_S', '02/02/2023', 75.00);
INSERT INTO bookings VALUES (12, 'DESERT_S', '02/04/2023', 85.00);

INSERT INTO bookings VALUES (13, 'DESERT_S', '02/05/2023', 75.00);
INSERT INTO bookings VALUES (14, 'DESERT_S', '02/06/2023', 34.00);
INSERT INTO bookings VALUES (15, 'DESERT_S', '02/09/2023', 85.00);

INSERT INTO bookings VALUES (16, 'DESERT_S', '02/12/2023', 23.00);
INSERT INTO bookings VALUES (17, 'DESERT_S', '02/13/2023', 76.00);
INSERT INTO bookings VALUES (18, 'DESERT_S', '02/14/2023', 85.00);

INSERT INTO bookings VALUES (19, 'OCEAN_WV', '02/01/2023', 98.00);
INSERT INTO bookings VALUES (20, 'OCEAN_WV', '02/02/2023', 75.00);
INSERT INTO bookings VALUES (21, 'OCEAN_WV', '02/04/2023', 85.00);

INSERT INTO bookings VALUES (22, 'OCEAN_WV', '02/06/2023', 75.00);
INSERT INTO bookings VALUES (23, 'OCEAN_WV', '02/09/2023', 34.00);
INSERT INTO bookings VALUES (24, 'OCEAN_WV', '02/12/2023', 85.00);

INSERT INTO bookings VALUES (25, 'OCEAN_WV', '02/13/2023', 23.00);
INSERT INTO bookings VALUES (26, 'OCEAN_WV', '02/14/2023', 76.00);
INSERT INTO bookings VALUES (27, 'OCEAN_WV', '02/16/2023', 85.00);

INSERT INTO bookings VALUES (28, 'CITY_BLD', '02/01/2023', 98.00);
INSERT INTO bookings VALUES (29, 'CITY_BLD', '02/02/2023', 75.00);
INSERT INTO bookings VALUES (30, 'CITY_BLD', '02/04/2023', 85.00);

INSERT INTO bookings VALUES (31, 'CITY_BLD', '02/12/2023', 75.00);
INSERT INTO bookings VALUES (32, 'CITY_BLD', '02/13/2023', 34.00);
INSERT INTO bookings VALUES (33, 'CITY_BLD', '02/17/2023', 85.00);

INSERT INTO bookings VALUES (34, 'CITY_BLD', '02/22/2023', 23.00);
INSERT INTO bookings VALUES (35, 'CITY_BLD', '02/23/2023', 76.00);
INSERT INTO bookings VALUES (36, 'CITY_BLD', '02/24/2023', 85.00);
```

 In this sample query, booking records are tallied to give a total for each week\. The end date for each week becomes a column name\.

```
SELECT * FROM
    (SELECT
       booking_id,
       (date_trunc('week', booking_date::date) + '5 days'::interval)::date as enddate,
       hotel_code AS "hotel code"
FROM bookings
) PIVOT (
    count(booking_id) FOR enddate IN ('2023-02-04','2023-02-11','2023-02-18') 
);
```

The query results in the following output\.

```
 hotel code | 2023-02-04  | 2023-02-11 | 2023-02-18
------------+-------------+------------+----------
 FOREST_L   |           3 |          2 |        1
 DESERT_S   |           4 |          3 |        2
 OCEAN_WV   |           3 |          3 |        3
 CITY_BLD   |           3 |          1 |        2
```

 Amazon Redshift doesn't support CROSSTAB to pivot on multiple columns\. But you can change row data to columns, in a similar manner to an aggregation with PIVOT, with a query like the following\. This uses the same booking sample data as the previous example\.

```
SELECT 
  booking_date,
  MAX(CASE WHEN hotel_code = 'FOREST_L' THEN 'forest is booked' ELSE '' END) AS FOREST_L,
  MAX(CASE WHEN hotel_code = 'DESERT_S' THEN 'desert is booked' ELSE '' END) AS DESERT_S,
  MAX(CASE WHEN hotel_code = 'OCEAN_WV' THEN 'ocean is booked' ELSE '' END)  AS OCEAN_WV
FROM bookings
GROUP BY booking_date
ORDER BY booking_date asc;
```

The sample query results in booking dates listed next to short phrases that indicate which hotels are booked\.

```
 booking_date  | forest_l         | desert_s         | ocean_wv
---------------+------------------+------------------+--------------------
 2023-02-01    | forest is booked | desert is booked |  ocean is booked
 2023-02-02    | forest is booked | desert is booked |  ocean is booked
 2023-02-04    | forest is booked | desert is booked |  ocean is booked
 2023-02-05    |                  | desert is booked |        
 2023-02-06    |                  | desert is booked |
```

The following are usage notes for `PIVOT`:
+ `PIVOT` can be applied to tables, sub\-queries, and common table expressions \(CTEs\)\. `PIVOT` cannot be applied to any `JOIN` expressions, recursive CTEs, `PIVOT`, or `UNPIVOT` expressions\. Also not supported are `SUPER` unnested expressions and Redshift Spectrum nested tables\.
+  `PIVOT` supports the `COUNT`, `SUM`, `MIN`, `MAX`, and `AVG` aggregate functions\. 
+ The `PIVOT` aggregate expression has to be a call of a supported aggregate function\. Complex expressions on top of the aggregate are not supported\. The aggregate arguments cannot contain references to tables other than the `PIVOT` input table\. Correlated references to a parent query are also not supported\. The aggregate argument may contain sub\-queries\. These can be correlated internally or on the `PIVOT` input table\.
+  The `PIVOT IN` list values cannot be column references or sub\-queries\. Each value must be type compatible with the `FOR` column reference\. 
+  If the `IN` list values do not have aliases, `PIVOT` generates default column names\. For constant `IN` values such as ‘abc’ or 5 the default column name is the constant itself\. For any complex expression, the column name is a standard Amazon Redshift default name such as `?column?`\. 

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