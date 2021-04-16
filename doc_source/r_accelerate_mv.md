# Accelerating PartiQL queries<a name="r_accelerate_mv"></a>

You can use materialized views to accelerate PartiQL queries that navigate and/or unnest hierarchical data in SUPER columns\. Create one or more materialized views to shred the SUPER values into multiple columns and utilize the columnar organization of Amazon Redshift analytical queries\. Consequently, queries make use of the materialized views\.

The materialized view essentially extracts and normalizes the nested data\. The level of normalization depends on how much effort you put into turning the SUPER data into conventional columnar data\. 