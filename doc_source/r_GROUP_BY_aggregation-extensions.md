# Aggregation extensions<a name="r_GROUP_BY_aggregation-extensions"></a>

Amazon Redshift supports aggregation extensions to do the work of multiple GROUP BY operations in a single statement\.

 The examples for aggregation extensions use the `orders` table, which holds sales data for an electronics company\. You can create `orders` with the following\.

```
CREATE TABLE ORDERS (
    ID INT,
    PRODUCT CHAR(20),
    CATEGORY CHAR(20),
    PRE_OWNED CHAR(1),
    COST DECIMAL
);

INSERT INTO ORDERS VALUES
    (0, 'laptop',       'computers',    'T', 1000),
    (1, 'smartphone',   'cellphones',   'T', 800),
    (2, 'smartphone',   'cellphones',   'T', 810),
    (3, 'laptop',       'computers',    'F', 1050),
    (4, 'mouse',        'computers',    'F', 50);
```

## *GROUPING SETS*<a name="r_GROUP_BY_aggregation-extensions-grouping-sets"></a>

 Computes one or more grouping sets in a single statement\. A grouping set is the set of a single GROUP BY clause, a set of 0 or more columns by which you can group a query's result set\. GROUP BY GROUPING SETS is equivalent to running a UNION ALL query on one result set grouped by different columns\. For example, GROUP BY GROUPING SETS\(\(a\), \(b\)\) is equivalent to GROUP BY a UNION ALL GROUP BY b\. 

 The following example returns the cost of the order table's products grouped according to both the products' categories and the kind of products sold\. 

```
SELECT category, product, sum(cost) as total
FROM orders
GROUP BY GROUPING SETS(category, product);

       category       |       product        | total
----------------------+----------------------+-------
 computers            |                      |  2100
 cellphones           |                      |  1610
                      | laptop               |  2050
                      | smartphone           |  1610
                      | mouse                |    50

(5 rows)
```

## *ROLLUP*<a name="r_GROUP_BY_aggregation-extensions-rollup"></a>

 Assumes a hierarchy where preceding columns are considered the parents of subsequent columns\. ROLLUP groups data by the provided columns, returning extra subtotal rows representing the totals throughout all levels of grouping columns, in addition to the grouped rows\. For example, you can use GROUP BY ROLLUP\(\(a\), \(b\)\) to return a result set grouped first by a, then by b while assuming that b is a subsection of a\. ROLLUP also returns a row with the whole result set without grouping columns\. 

GROUP BY ROLLUP\(\(a\), \(b\)\) is equivalent to GROUP BY GROUPING SETS\(\(a,b\), \(a\), \(\)\)\. 

The following example returns the cost of the order table's products grouped first by category and then product, with product as a subdivision of category\.

```
SELECT category, product, sum(cost) as total
FROM orders
GROUP BY ROLLUP(category, product) ORDER BY 1,2;

       category       |       product        | total
----------------------+----------------------+-------
 cellphones           | smartphone           |  1610
 cellphones           |                      |  1610
 computers            | laptop               |  2050
 computers            | mouse                |    50
 computers            |                      |  2100
                      |                      |  3710
(6 rows)
```

## *CUBE*<a name="r_GROUP_BY_aggregation-extensions-cube"></a>

 Groups data by the provided columns, returning extra subtotal rows representing the totals throughout all levels of grouping columns, in addition to the grouped rows\. CUBE returns the same rows as ROLLUP, while adding additional subtotal rows for every combination of grouping column not covered by ROLLUP\. For example, you can use GROUP BY CUBE \(\(a\), \(b\)\) to return a result set grouped first by a, then by b while assuming that b is a subsection of a, then by b alone\. CUBE also returns a row with the whole result set without grouping columns\.

GROUP BY CUBE\(\(a\), \(b\)\) is equivalent to GROUP BY GROUPING SETS\(\(a, b\), \(a\), \(b\), \(\)\)\. 

The following example returns the cost of the order table's products grouped first by category and then product, with product as a subdivision of category\. Unlike the preceding example for ROLLUP, the statement returns results for every combination of grouping column\. 

```
SELECT category, product, sum(cost) as total
FROM orders
GROUP BY CUBE(category, product) ORDER BY 1,2;

       category       |       product        | total
----------------------+----------------------+-------
 cellphones           | smartphone           |  1610
 cellphones           |                      |  1610
 computers            | laptop               |  2050
 computers            | mouse                |    50
 computers            |                      |  2100
                      | laptop               |  2050
                      | mouse                |    50
                      | smartphone           |  1610
                      |                      |  3710
(9 rows)
```

## *GROUPING/GROUPING\_ID functions*<a name="r_GROUP_BY_aggregation-extentions-grouping"></a>

 ROLLUP and CUBE add NULL values to the result set to indicate subtotal rows\. For example, GROUP BY ROLLUP\(\(a\), \(b\)\) returns one or more rows that have a value of NULL in the b grouping column to indicate they are subtotals of fields in the a grouping column\. These NULL values serve only to satisfy the format of returning tuples\.

 When you run GROUP BY operations with ROLLUP and CUBE on relations that store NULL values themselves, this can produce result sets with rows that appear to have identical grouping columns\. Returning to the previous example, if the b grouping column contains a stored NULL value, GROUP BY ROLLUP\(\(a\), \(b\)\) returns a row with a value of NULL in the b grouping column that isn't a subtotal\. 

 To distinguish between NULL values created by ROLLUP and CUBE, and the NULL values stored in the tables themselves, you can use the GROUPING function, or its alias GROUPING\_ID\. GROUPING takes a single grouping set as its argument, and for each row in the result set returns a 0 or 1 bit value corresponding to the grouping column in that position, and then converts that value into an integer\. If the value in that position is a NULL value created by an aggregation extension, GROUPING returns 1\. It returns 0 for all other values, including stored NULL values\.

 For example, GROUPING\(category, product\) can return the following values for a given row, depending on the grouping column values for that row\. For the purposes of this example, all NULL values in the table are NULL values created by an aggregation extension\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_GROUP_BY_aggregation-extensions.html)

GROUPING functions appear in the SELECT list portion of the query in the following format\.

```
SELECT ... [GROUPING( expr )...] ...
  GROUP BY ... {CUBE | ROLLUP| GROUPING SETS} ( expr ) ...
```

The following example is the same as the preceding example for CUBE, but with the addition of GROUPING functions for its grouping sets\.

```
SELECT category, product,
       GROUPING(category) as grouping0,
       GROUPING(product) as grouping1,
       GROUPING(category, product) as grouping2,
       sum(cost) as total
FROM orders
GROUP BY CUBE(category, product) ORDER BY 3,1,2;

       category       |       product        | grouping0 | grouping1 | grouping2 | total
----------------------+----------------------+-----------+-----------+-----------+-------
 cellphones           | smartphone           |         0 |         0 |         0 |  1610
 cellphones           |                      |         0 |         1 |         1 |  1610
 computers            | laptop               |         0 |         0 |         0 |  2050
 computers            | mouse                |         0 |         0 |         0 |    50
 computers            |                      |         0 |         1 |         1 |  2100
                      | laptop               |         1 |         0 |         2 |  2050
                      | mouse                |         1 |         0 |         2 |    50
                      | smartphone           |         1 |         0 |         2 |  1610
                      |                      |         1 |         1 |         3 |  3710
(9 rows)
```

## *Partial ROLLUP and CUBE*<a name="r_GROUP_BY_aggregation-extentions-partial"></a>

 You can run ROLLUP and CUBE operations with only a portion of the subtotals\. 

 The syntax for partial ROLLUP and CUBE operations is as follows\.

```
GROUP BY expr1, { ROLLUP | CUBE }(expr2, [, ...])
```

Here, the GROUP BY clause only creates subtotal rows at the level of *expr2* and onwards\.

The following examples show partial ROLLUP and CUBE operations on the orders table, grouping first by whether a product is pre\-owned and then running ROLLUP and CUBE on the category and product columns\.

```
SELECT pre_owned, category, product,
       GROUPING(category, product, pre_owned) as group_id,
       sum(cost) as total
FROM orders
GROUP BY pre_owned, ROLLUP(category, product) ORDER BY 4,1,2,3;

 pre_owned |       category       |       product        | group_id | total
-----------+----------------------+----------------------+----------+-------
 F         | computers            | laptop               |        0 |  1050
 F         | computers            | mouse                |        0 |    50
 T         | cellphones           | smartphone           |        0 |  1610
 T         | computers            | laptop               |        0 |  1000
 F         | computers            |                      |        2 |  1100
 T         | cellphones           |                      |        2 |  1610
 T         | computers            |                      |        2 |  1000
 F         |                      |                      |        6 |  1100
 T         |                      |                      |        6 |  2610
(9 rows)

SELECT pre_owned, category, product,
       GROUPING(category, product, pre_owned) as group_id,
       sum(cost) as total
FROM orders
GROUP BY pre_owned, CUBE(category, product) ORDER BY 4,1,2,3;

 pre_owned |       category       |       product        | group_id | total
-----------+----------------------+----------------------+----------+-------
 F         | computers            | laptop               |        0 |  1050
 F         | computers            | mouse                |        0 |    50
 T         | cellphones           | smartphone           |        0 |  1610
 T         | computers            | laptop               |        0 |  1000
 F         | computers            |                      |        2 |  1100
 T         | cellphones           |                      |        2 |  1610
 T         | computers            |                      |        2 |  1000
 F         |                      | laptop               |        4 |  1050
 F         |                      | mouse                |        4 |    50
 T         |                      | laptop               |        4 |  1000
 T         |                      | smartphone           |        4 |  1610
 F         |                      |                      |        6 |  1100
 T         |                      |                      |        6 |  2610
(13 rows)
```

Since the pre\-owned column isn't included in the ROLLUP and CUBE operations, there's no grand total row that includes all other rows\. 

## *Concatenated grouping*<a name="r_GROUP_BY_aggregation-extentions-concat"></a>

 You can concatenate multiple GROUPING SETS/ROLLUP/CUBE clauses to calculate different levels of subtotals\. Concatenated groupings return the Cartesian product of the provided grouping sets\. 

 The syntax for concatenating GROUPING SETS/ROLLUP/CUBE clauses is as follows\.

```
GROUP BY {ROLLUP|CUBE|GROUPING SETS}(expr1[, ...]),
         {ROLLUP|CUBE|GROUPING SETS}(expr1[, ...])[, ...]
```

Consider the following example to see how a small concatenated grouping can produce a large final result set\.

```
SELECT pre_owned, category, product,
       GROUPING(category, product, pre_owned) as group_id,
       sum(cost) as total
FROM orders
GROUP BY CUBE(category, product), GROUPING SETS(pre_owned, ())
ORDER BY 4,1,2,3;

 pre_owned |       category       |       product        | group_id | total
-----------+----------------------+----------------------+----------+-------
 F         | computers            | laptop               |        0 |  1050
 F         | computers            | mouse                |        0 |    50
 T         | cellphones           | smartphone           |        0 |  1610
 T         | computers            | laptop               |        0 |  1000
           | cellphones           | smartphone           |        1 |  1610
           | computers            | laptop               |        1 |  2050
           | computers            | mouse                |        1 |    50
 F         | computers            |                      |        2 |  1100
 T         | cellphones           |                      |        2 |  1610
 T         | computers            |                      |        2 |  1000
           | cellphones           |                      |        3 |  1610
           | computers            |                      |        3 |  2100
 F         |                      | laptop               |        4 |  1050
 F         |                      | mouse                |        4 |    50
 T         |                      | laptop               |        4 |  1000
 T         |                      | smartphone           |        4 |  1610
           |                      | laptop               |        5 |  2050
           |                      | mouse                |        5 |    50
           |                      | smartphone           |        5 |  1610
 F         |                      |                      |        6 |  1100
 T         |                      |                      |        6 |  2610
           |                      |                      |        7 |  3710
(22 rows)
```

## *Nested grouping*<a name="r_GROUP_BY_aggregation-extentions-nested"></a>

 You can use GROUPING SETS/ROLLUP/CUBE operations as your GROUPING SETS *expr* to form a nested grouping\. The sub grouping inside nested GROUPING SETS is flattened\. 

 The syntax for nested grouping is as follows\.

```
GROUP BY GROUPING SETS({ROLLUP|CUBE|GROUPING SETS}(expr[, ...])[, ...])
```

Consider the following example\.

```
SELECT category, product, pre_owned,
       GROUPING(category, product, pre_owned) as group_id,
       sum(cost) as total
FROM orders
GROUP BY GROUPING SETS(ROLLUP(category), CUBE(product, pre_owned))
ORDER BY 4,1,2,3;

       category       |       product        | pre_owned | group_id | total
----------------------+----------------------+-----------+----------+-------
 cellphones           |                      |           |        3 |  1610
 computers            |                      |           |        3 |  2100
                      | laptop               | F         |        4 |  1050
                      | laptop               | T         |        4 |  1000
                      | mouse                | F         |        4 |    50
                      | smartphone           | T         |        4 |  1610
                      | laptop               |           |        5 |  2050
                      | mouse                |           |        5 |    50
                      | smartphone           |           |        5 |  1610
                      |                      | F         |        6 |  1100
                      |                      | T         |        6 |  2610
                      |                      |           |        7 |  3710
                      |                      |           |        7 |  3710
(13 rows)
```

Note that because both ROLLUP\(category\) and CUBE\(product, pre\_owned\) contain the grouping set \(\), the row representing the grand total is duplicated\.

## *Usage notes*<a name="r_GROUP_BY_aggregation-extensions-usage-notes"></a>
+ The GROUP BY clause supports up to 64 grouping sets\. In the case of ROLLUP and CUBE, or some combination of GROUPING SETS, ROLLUP, and CUBE, this limitation applies to the implied number of grouping sets\. For example, GROUP BY CUBE\(\(a\), \(b\)\) counts as 4 grouping sets, not 2\.
+ You can't use constants as grouping columns when using aggregation extensions\.
+ You can't make a grouping set that contains duplicate columns\.