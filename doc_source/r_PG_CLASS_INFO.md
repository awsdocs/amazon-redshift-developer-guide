# PG\_CLASS\_INFO<a name="r_PG_CLASS_INFO"></a>

PG\_CLASS\_INFO is an Amazon Redshift system view built on the PostgreSQL catalog tables PG\_CLASS and PG\_CLASS\_EXTENDED\. PG\_CLASS\_INFO includes details about table creation time and the current distribution style\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)\.

PG\_CLASS\_INFO is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_PG_CLASS_INFO-table-columns2"></a>

PG\_CLASS\_INFO shows the following columns in addition to the columns in PG\_CLASS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_CLASS_INFO.html)

The RELEFFECTIVEDISTSTYLE column in PG\_CLASS\_INFO indicates the current distribution style for the table\. If the table uses automatic distribution, RELEFFECTIVEDISTSTYLE is 10 or 11, which indicates whether the effective distribution style is AUTO \(ALL\) or AUTO \(EVEN\)\. If the table uses automatic distribution, the distribution style might initially show AUTO \(ALL\), then change to AUTO \(EVEN\) when the table grows\. 

The following table gives the distribution style for each value in RELEFFECTIVEDISTSTYLE column: 


| RELEFFECTIVEDISTSTYLE | Current distribution style | 
| --- | --- | 
| 0 | EVEN | 
| 1 | KEY | 
| 8 | ALL | 
| 10 | AUTO \(ALL\) | 
| 11 | AUTO \(EVEN\) | 

## Example<a name="r_PG_CLASS_INFO-example"></a>

The following query returns the current distribution style of tables in the catalog\. 

```
select reloid as tableid,trim(nspname) as schemaname,trim(relname) as tablename,reldiststyle,releffectivediststyle, 
CASE WHEN "reldiststyle" = 0 THEN 'EVEN'::text 
     WHEN "reldiststyle" = 1 THEN 'KEY'::text 
     WHEN "reldiststyle" = 8 THEN 'ALL'::text 
     WHEN "releffectivediststyle" = 10 THEN 'AUTO(ALL)'::text 
     WHEN "releffectivediststyle" = 11 THEN 'AUTO(EVEN)'::text ELSE '<<UNKNOWN>>'::text END as diststyle,relcreationtime 
from pg_class_info a left join pg_namespace b on a.relnamespace=b.oid;
```

```
 tableid | schemaname | tablename | reldiststyle | releffectivediststyle | diststyle  |      relcreationtime       
---------+------------+-----------+--------------+-----------------------+------------+----------------------------
 3638033 | public     | customer  |            0 |                     0 | EVEN       | 2019-06-13 15:02:50.666718
 3638037 | public     | sales     |            1 |                     1 | KEY        | 2019-06-13 15:03:29.595007
 3638035 | public     | lineitem  |            8 |                     8 | ALL        | 2019-06-13 15:03:01.378538
 3638039 | public     | product   |            9 |                    10 | AUTO(ALL)  | 2019-06-13 15:03:42.691611
 3638041 | public     | shipping  |            9 |                    11 | AUTO(EVEN) | 2019-06-13 15:03:53.69192
(5 rows)
```