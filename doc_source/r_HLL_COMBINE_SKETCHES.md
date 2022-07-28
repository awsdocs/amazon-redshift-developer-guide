# HLL\_COMBINE\_SKETCHES function<a name="r_HLL_COMBINE_SKETCHES"></a>

The HLL\_COMBINE\_SKETCHES is a scalar function that takes as input two HLLSKETCH values and combines them into a single HLLSKETCH\.

The combination of two or more HyperLogLog sketches is a new HLLSKETCH that encapsulates information about the union of the distinct values that each input sketch represents\.

## Syntax<a name="r_HLL_COMBINE_SKETCHES-synopsis"></a>

```
HLL_COMBINE_SKETCHES (hllsketch_expression1, hllsketch_expression2)
```

## Argument<a name="r_HLL_COMBINE_SKETCHES-argument"></a>

 *hllsketch\_expression1* and *hllsketch\_expression2*   
Any valid expression that evaluates to an HLLSKETCH type, such as a column name\.

## Return type<a name="r_HLL_COMBINE_SKETCHES-return-type"></a>

The HLL\_COMBINE\_SKETCHES function returns an HLLSKETCH type\.

## Examples<a name="r_HLL_COMBINE_SKETCHES-examples"></a>

The following example returns the combined HLLSKETCH values in the table `hll_table`\.

```
WITH tbl1(x, y)
     AS (SELECT Hll_create_sketch(1),
                Hll_create_sketch(2)
         UNION ALL
         SELECT Hll_create_sketch(3),
                Hll_create_sketch(4)
         UNION ALL
         SELECT Hll_create_sketch(5),
                Hll_create_sketch(6)
         UNION ALL
         SELECT Hll_create_sketch(7),
                Hll_create_sketch(8)),
     tbl2(x, y)
     AS (SELECT Hll_create_sketch(9),
                Hll_create_sketch(10)
         UNION ALL
         SELECT Hll_create_sketch(11),
                Hll_create_sketch(12)
         UNION ALL
         SELECT Hll_create_sketch(13),
                Hll_create_sketch(14)
         UNION ALL
         SELECT Hll_create_sketch(15),
                Hll_create_sketch(16)
         UNION ALL
         SELECT Hll_create_sketch(NULL),
                Hll_create_sketch(NULL)),
     tbl3(x, y)
     AS (SELECT *
         FROM   tbl1
         UNION ALL
         SELECT *
         FROM   tbl2)
SELECT Hll_combine_sketches(x, y)
FROM   tbl3;
```