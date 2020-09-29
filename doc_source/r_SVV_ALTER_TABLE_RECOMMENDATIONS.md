# SVV\_ALTER\_TABLE\_RECOMMENDATIONS<a name="r_SVV_ALTER_TABLE_RECOMMENDATIONS"></a>

Records the current Amazon Redshift Advisor recommendations for tables\. This view shows recommendations for all tables, whether they are defined for automatic optimization or not\. To view if a table is defined for automatic optimization, see [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md)\. Entries appear only for tables visible in the current session's database\. After a recommendation has been applied \(either by Amazon Redshift or by you\), it no longer appears in the view\. 

SVV\_ALTER\_TABLE\_RECOMMENDATIONS is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVV_ALTER_TABLE_RECOMMENDATIONS-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALTER_TABLE_RECOMMENDATIONS.html)

## Sample queries<a name="r_SVV_ALTER_TABLE_RECOMMENDATIONS-sample-queries"></a>

In the following example, the rows in the result show recommendations for distribution key and sort key\. The rows also show whether the recommendations are eligible for Amazon Redshift to automatically apply them\. 

```
select type, database, table_id, group_id, ddl, auto_eligible 
from svv_alter_table_recommendations;
```

```
 type      | database | table_id | group_id | ddl                                                                                                                                                 | auto_eligible
 diststyle | db0      | 117884   | 2        | ALTER TABLE "sch"."dp21235_tbl_1" ALTER DISTSTYLE KEY DISTKEY "c0"                                                                                  | f
 diststyle | db0      | 117892   | 2        | ALTER TABLE "sch"."dp21235_tbl_1" ALTER DISTSTYLE KEY DISTKEY "c0"                                                                                  | f
 diststyle | db0      | 117885   | 1        | ALTER TABLE "sch"."catalog_returns" ALTER DISTSTYLE KEY DISTKEY "cr_sold_date_sk", ALTER COMPOUND SORTKEY ("cr_sold_date_sk","cr_returned_time_sk") | t
 sortkey   | db0      | 117890   | -1       | ALTER TABLE "sch"."customer_addresses" ALTER COMPOUND SORTKEY ("ca_address_sk")                                                                     | t
```