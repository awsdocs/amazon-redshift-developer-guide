# PG\_PROC\_INFO<a name="r_PG_PROC_INFO"></a>

PG\_PROC\_INFO is an Amazon Redshift system view built on the PostgreSQL catalog table PG\_PROC and the internal catalog table PG\_PROC\_EXTENDED\. PG\_PROC\_INFO includes details about stored procedures and functions, including information related to output arguments, if any\.

## Table columns<a name="r_PG_PROC_INFO-table-columns"></a>

PG\_PROC\_INFO shows the following columns in addition to the columns in PG\_PROC\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_PROC_INFO.html)

The field proargnames in PG\_PROC\_INFO contains the names of all types of arguments \(including OUT and INOUT\), if any\.