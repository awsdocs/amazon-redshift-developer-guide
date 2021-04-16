# SVL\_FEDERATED\_QUERY<a name="r_SVL_FEDERATED_QUERY"></a>

Use the SVL\_FEDERATED\_QUERY view to view information about a federated query call\.

SVL\_FEDERATED\_QUERY is visible to all users\. Superusers can see all rows\. Regular users can see only their own data\.

## Table columns<a name="SVL_FEDERATED_QUERY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_FEDERATED_QUERY.html)

## Sample queries<a name="SVL_FEDERATED_QUERY-sample-queries"></a>

To show information about federated query calls, run the following query\.

```
select query, trim(sourcetype) as type, recordtime, trim(querytext) as "PG Subquery" from svl_federated_query where query = 4292;
                
 query | type |         recordtime         |                          pg subquery
-------+------+----------------------------+---------------------------------------------------------------
  4292 | PG   | 2020-03-27 04:29:58.485126 | SELECT "level" FROM functional.employees WHERE ("level" >= 6)
(1 row)
```