# Creating Amazon Redshift scalar columns out of shredded data<a name="r_create_scalar"></a>

Schemaless data stored in SUPER can affect the performance of Amazon Redshift\. For instance, filter predicates or join conditions as range\-restricted scans can't effectively use zone maps\. Users and BI tools can use materialized views as the conventional presentation of the data and increase performance of analytical queries\.

The following query scans the materialized view `super_mv` and filters on `o_orderstatus`\.

```
SELECT c.c_name, v.o_totalprice
FROM customer_orders_lineitem c
JOIN super_mv v ON c.c_custkey = v.c_custkey
WHERE v.o_orderstatus = 'F';
```

Inspect `stl_scan` to verify that Amazon Redshift can't effectively use zone maps on the range\-restricted scan over `o_orderstatus`\.

```
SELECT slice, is_rrscan FROM stl_scan
WHERE query = pg_last_query_id() AND perm_table_name LIKE '%super_mv%';

 slice | is_rrscan 
-------+-----------
     0 | t
     1 | t
     5 | t
     4 | t
     2 | t
     3 | t
(6 rows)
```

The following example adapts the materialized view `super_mv` to create scalar columns out of the shredded data\. In this case, Amazon Redshift casts `o_orderstatus` from SUPER to VARCHAR\. In addition, specify `o_orderstatus` as the sort key for `super_mv`\.

```
CREATE MATERIALIZED VIEW super_mv distkey(c_custkey) sortkey(c_custkey, o_orderstatus) AS (
  SELECT c_custkey, o.o_orderstatus::VARCHAR AS o_orderstatus, o.o_totalprice, o_idx
  FROM customer_orders_lineitem c, c.c_orders o AT o_idx
);
```

After re\-running the query, verify that Amazon Redshift can now use zone maps\.

```
SELECT v.o_totalprice
FROM super_mv v
WHERE v.o_orderstatus = 'F';
```

You can verify that the range\-restricted scan now uses zone maps as follows\.

```
SELECT slice, is_rrscan FROM stl_scan
WHERE query = pg_last_query_id() AND perm_table_name LIKE '%super_mv%';

 slice | is_rrscan 
-------+-----------
     0 | t
     1 | t
     2 | t
     3 | t
     4 | t
     5 | t
(6 rows)
```