# Examples<a name="r_HLL-examples"></a>

## Example: Return cardinality in a subquery<a name="hll-examples-subquery"></a>

The following example returns the cardinality for each sketch in a subquery for a table named *Sales*\.

```
CREATE TABLE Sales (customer VARCHAR, country VARCHAR, amount BIGINT);
INSERT INTO Sales VALUES ('David Joe', 'Greece', 14.5),  ('David Joe', 'Greece', 19.95), ('John Doe', 'USA', 29.95), ('John Doe', 'USA', 19.95), ('George Spanos', 'Greece', 9.95), ('George Spanos', 'Greece', 2.95);
```

The following query generates an HLL sketch for the customers of each country and extracts the cardinality\. This shows unique customers from each country\.

```
SELECT hll_cardinality(sketch), country
FROM (SELECT hll_create_sketch(customer) AS sketch, country
        FROM Sales
        GROUP BY country) AS hll_subquery;
        
hll_cardinality | country
----------------+---------
            1   | USA
            2   | Greece
 ...
```

## Example: Return an HLLSKETCH type from combined sketches in a subquery<a name="hll-examples-combined-subquery"></a>

The following example returns a single HLLSKETCH type that represents the combination of individual sketches from a subquery\. The sketches are combined by using the HLL\_COMBINE aggregate function\. 

```
SELECT hll_combine(sketch)
FROM (SELECT hll_create_sketch(customers) AS sketch
        FROM Sales
        GROUP BY country) AS hll_subquery
       
                                        hll_combine
--------------------------------------------------------------------------------------------
 {"version":1,"logm":15,"sparse":{"indices":[29808639,35021072,47612452],"values":[1,1,1]}}
(1 row)
```

## Example: Return a HyperLogLog sketch from combining multiple sketches<a name="hll-examples-multiple-sketches"></a>

For the following example, suppose that the table `page-users` stores preaggregated sketches for each page that users visited on a given website\. Each row in this table contains a HyperLogLog sketch that represents all user IDs that show the visited pages\.

```
page_users
-- +----------------+-------------+--------------+
-- | _PARTITIONTIME | page         | sketch |
-- +----------------+-------------+--------------+
-- | 2019-07-28     | homepage     | CHAQkAQYA... |
-- | 2019-07-28     | Product A    | CHAQxPnYB... |
-- +----------------+-------------+--------------+
```

The following example unions the preaggregated multiple sketches and generates a single sketch\. This sketch encapsulates the collective cardinality that each sketch encapsulates\.

```
SELECT hll_combine(sketch) as sketch
FROM page_users
```

The output looks similar to the following\.

```
-- +-----------------------------------------+
-- | sketch |
-- +-----------------------------------------+
-- | CHAQ3sGoCxgCIAuCB4iAIBgTIBgqgIAgAwY.... |
-- +-----------------------------------------+
```

When a new sketch is created, you can use the HLL\_CARDINALITY function to get the collective distinct values, as shown following\.

```
SELECT hll_cardinality(sketch)
FROM ( 
  SELECT
  hll_combine(sketch) as sketch
  FROM page_users
) AS hll_subquery
```

The output looks similar to the following\.

```
-- +-------+
-- | count |
-- +-------+
-- | 54356 |
-- +-------+
```

## Example: Cache HyperLogLog sketches for cardinality estimation<a name="hll-examples-cache-sketches"></a>

The following examples cache HyperLogLog sketches to avoid directly accessing Amazon S3 for cardinality estimation\. 

You can preaggregate and cache HyperLogLog sketches in external tables defined to hold Amazon S3 data\. By doing this, you can extract cardinality estimates without accessing the underlying base data\. 

For example, suppose that you have unloaded a set of tab\-delimited text files into Amazon S3\. You run the following query to define an external table named `sales` in the Amazon Redshift external schema named `spectrum`\. 

```
create external table spectrum.sales(
sellerid integer,
buyerid integer,
dateid smallint,
qtysold smallint,
product_type integer,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime date)
row format delimited
fields terminated by '\t'stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales/';
```

Suppose that you want to compute the distinct buyers who purchased an item on arbitrary dates\. To do so, the following example generates sketches for the buyer IDs for each day of the year and stores the result in the Amazon Redshift table `hll_sales`\.

```
CREATE TABLE hll_sales AS
SELECT saletime, hll_create_sketch(buyerid) AS sketch
FROM spectrum.sales
GROUP BY saletime;
```

The output looks similar to the following\.

```
-- hll_sales

-- | saletime       | sketch        |
-- +----------------+---------------+
-- | 2018-11-23     | "CHAQkAQYA..."
-- | 2018-11-24     | "TNLMLMLKK..."
-- | 2018-11-25     | "KMNKLLOKM..."
-- | 2018-11-26     | "MMKNKLLMO..."
-- | 2018-11-27     | "MMLSKNLPM..."
-- +----------------+---------------+
```

The following query extracts the estimated number of distinct buyers that purchased an item during the Friday after Thanksgiving\.

```
SELECT hll_cardinality(sketch) as distinct_buyers
FROM hll_sales
WHERE saletime = '2018-11-23';
```

The output looks similar to the following\.

```
distinct_buyers
---------------
1771
```

Suppose that you want the number of distinct users who bought an item on a certain range of dates\. An example might be from the Friday after Thanksgiving to the following Monday\. To get this, the following query uses the `hll_combine` aggregate function\. This function enables you to avoid double\-counting buyers who purchased an item on more than one day of the selected range\. 

```
SELECT hll_cardinality(hll_combine(sketch)) as distinct_buyers
FROM hll_sales
WHERE saletime BETWEEN '2018-11-23' AND '2018-11-26';
```

The output looks similar to the following\.

```
distinct_buyers
---------------
232152
```

To keep the `hll_sales` table up\-to\-date, run the following query at the end of each day\. Doing this generates an HyperLogLog sketch based on the IDs of buyers that purchased an item today and adds it to the `hll_sales` table\.

```
INSERT INTO hll_sales 
SELECT saletime, hll_create_sketch(buyerid) 
FROM spectrum.sales 
WHERE saletime = to_char(now(), 'YYYY-MM-DD');
```