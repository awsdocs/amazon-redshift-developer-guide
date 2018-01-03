# SVL\_STATEMENTTEXT<a name="r_SVL_STATEMENTTEXT"></a>

Use the SVL\_STATEMENTTEXT view to get a complete record of all of the SQL commands that have been run on the system\.

The SVL\_STATEMENTTEXT view contains the union of all of the rows in the [STL\_DDLTEXT](r_STL_DDLTEXT.md), [STL\_QUERYTEXT](r_STL_QUERYTEXT.md), and [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md) tables\. This view also includes a join to the STL\_QUERY table\.

SVL\_STATEMENTTEXT is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVL_STATEMENTTEXT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_STATEMENTTEXT.html)

## Sample Query<a name="r_SVL_STATEMENTTEXT-sample-query"></a>

The following query returns DDL statements that were run on June 16th, 2009: 

```
select starttime, type, rtrim(text) from svl_statementtext
where starttime like '2009-06-16%' and type='DDL' order by starttime asc;

starttime                  | type |              rtrim
---------------------------|------|--------------------------------
2009-06-16 10:36:50.625097 | DDL  | create table ddltest(c1 int);
2009-06-16 15:02:16.006341 | DDL  | drop view alltickitjoin;
2009-06-16 15:02:23.65285  | DDL  | drop table sales;
2009-06-16 15:02:24.548928 | DDL  | drop table listing;
2009-06-16 15:02:25.536655 | DDL  | drop table event;
...
```