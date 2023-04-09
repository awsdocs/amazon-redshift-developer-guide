# SVV\_MV\_DEPENDENCY<a name="r_SVV_MV_DEPENDENCY"></a>

The SVV\_MV\_DEPENDENCY table shows the dependencies of materialized views on other materialized views within Amazon Redshift\. 

For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

SVV\_MV\_DEPENDENCY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVV_MV_DEPENDENCY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_MV_DEPENDENCY.html)

## Sample query<a name="r_SVV_MV_DEPENDENCY-sample-query"></a>

The following query returns an output row that indicates that the materialized view `mv_over_foo` uses the materialized view `mv_foo` in its definition as a dependency\.

```
CREATE SCHEMA test_ivm_setup;
CREATE TABLE test_ivm_setup.foo(a INT);
CREATE MATERIALIZED VIEW test_ivm_setup.mv_foo AS SELECT * FROM test_ivm_setup.foo;
CREATE MATERIALIZED VIEW test_ivm_setup.mv_over_foo AS SELECT * FROM test_ivm_setup.mv_foo;

SELECT * FROM svv_mv_dependency;
                
 database_name | schema_name          | name        | dependent_database_name | dependent_schema_name     | dependent_name 
---------------+----------------------+-------------+-------------------------+---------------------------+----------
 dev           | test_ivm_setup       | mv_over_foo |                     dev | test_ivm_setup            | mv_foo
```