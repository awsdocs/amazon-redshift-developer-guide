# STV\_EXEC\_STATE<a name="r_STV_EXEC_STATE"></a>

Use the STV\_EXEC\_STATE table to find out information about queries and query steps that are actively running on compute nodes\.

This information is usually used only to troubleshoot engineering issues\. The views SVV\_QUERY\_STATE and SVL\_QUERY\_SUMMARY extract their information from STV\_EXEC\_STATE\.

STV\_EXEC\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_EXEC_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_EXEC_STATE.html)

## Sample queries<a name="r_STV_EXEC_STATE-sample-queries"></a>

Rather than querying STV\_EXEC\_STATE directly, Amazon Redshift recommends querying SVL\_QUERY\_SUMMARY or SVV\_QUERY\_STATE to obtain the information in STV\_EXEC\_STATE in a more user\-friendly format\. See the [SVL\_QUERY\_SUMMARY](r_SVL_QUERY_SUMMARY.md) or [SVV\_QUERY\_STATE](r_SVV_QUERY_STATE.md) table documentation for more details\.