# Defining table constraints<a name="t_Defining_constraints"></a>

Uniqueness, primary key, and foreign key constraints are informational only; *they are not enforced by Amazon Redshift* when you populate a table\. For example, if you insert data into a table with dependencies, the insert can succeed even if it violates the constraint\. Nonetheless, primary keys and foreign keys are used as planning hints and they should be declared if your ETL process or some other process in your application enforces their integrity\.

For example, the query planner uses primary and foreign keys in certain statistical computations\. It does this to infer uniqueness and referential relationships that affect subquery decorrelation techniques\. By doing this, it can order large numbers of joins and remove redundant joins\.

The planner leverages these key relationships, but it assumes that all keys in Amazon Redshift tables are valid as loaded\. If your application allows invalid foreign keys or primary keys, some queries could return incorrect results\. For example, a SELECT DISTINCT query might return duplicate rows if the primary key is not unique\. Do not define key constraints for your tables if you doubt their validity\. However, always declare primary and foreign keys and uniqueness constraints when you know that they are valid\.

Amazon Redshift *does* enforce NOT NULL column constraints\.

For more information about table constraints, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\. For information about how to drop a table with dependencies, see [DROP TABLE](r_DROP_TABLE.md)\. 