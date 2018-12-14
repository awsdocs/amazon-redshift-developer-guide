# PG\_CLASS\_INFO<a name="r_PG_CLASS_INFO"></a>

PG\_CLASS\_INFO is an Amazon Redshift system view built on the PostgreSQL catalog tables PG\_CLASS and PG\_CLASS\_EXTENDED\. PG\_CLASS\_INFO includes details about table creation time and the current distribution style\. For more information, see [Choosing a Data Distribution Style](t_Distributing_data.md)\.

PG\_CLASS\_INFO is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_PG_CLASS_INFO-table-columns2"></a>

PG\_CLASS\_INFO shows the following columns in addition to the columns in PG\_CLASS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_CLASS_INFO.html)

The RELEFFECTIVEDISTSTYLE column in PG\_CLASS\_INFO indicates the current distribution style for the table\. If the table uses automatic distribution, RELEFFECTIVEDISTSTYLE is 10 or 11, which indicates whether the effective distribution style is AUTO \(ALL\) or AUTO \(EVEN\)\. If the table uses automatic distribution, the distribution style might initially show AUTO \(ALL\), then change to AUTO \(EVEN\) when the table grows\. 

The following table gives the distribution style for each value in RELEFFECTIVEDISTSTYLE column: 


| RELEFFECTIVEDISTSTYLE | Current Distribution style | 
| --- | --- | 
| 0 | EVEN | 
| 1 | KEY | 
| 8 | ALL | 
| 10 | AUTO \(ALL\) | 
| 11 | AUTO \(EVEN\) | 