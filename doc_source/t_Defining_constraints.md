# Defining constraints<a name="t_Defining_constraints"></a>

Uniqueness, primary key, and foreign key constraints are informational only; *they are not enforced by Amazon Redshift*\. Nonetheless, primary keys and foreign keys are used as planning hints and they should be declared if your ETL process or some other process in your application enforces their integrity\.

For example, the query planner uses primary and foreign keys in certain statistical computations, to infer uniqueness and referential relationships that affect subquery decorrelation techniques, to order large numbers of joins, and to eliminate redundant joins\.

The planner leverages these key relationships, but it assumes that all keys in Amazon Redshift tables are valid as loaded\. If your application allows invalid foreign keys or primary keys, some queries could return incorrect results\. For example, a SELECT DISTINCT query might return duplicate rows if the primary key is not unique\. Do not define key constraints for your tables if you doubt their validity\. On the other hand, you should always declare primary and foreign keys and uniqueness constraints when you know that they are valid\.

Amazon Redshift *does* enforce NOT NULL column constraints\.

For more information about table constraints, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\. 