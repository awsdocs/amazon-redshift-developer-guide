# Viewing Distribution Styles<a name="viewing-distribution-styles"></a>

To view the distribution style of a table, query the PG\_CLASS system catalog table\. The RELDISTSTYLE column indicates the distribution style for the table\. The following table gives the distribution style for each value in PG\_CLASS: 


| RELDISTSTYLE | Distribution style | 
| --- | --- | 
| 0 | EVEN | 
| 1 | KEY | 
| 8 | ALL | 

The following example creates three tables using the three distribution styles and then queries PG\_CLASS to view the distribution styles\. 

```
create table alldiststyle (col1 int)
diststyle all;

create table evendiststyle (col1 int)
diststyle even;

create table keydiststyle (col1 int)
diststyle key distkey (col1);

select relname, reldiststyle from pg_class
where relname like '%diststyle';

   relname    | reldiststyle
--------------+-------------
evendiststyle |  0
keydiststyle  |  1
alldiststyle  |  8
```