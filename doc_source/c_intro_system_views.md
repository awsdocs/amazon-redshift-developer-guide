# System Views<a name="c_intro_system_views"></a>

System views contain a subset of data found in several of the STL and STV system tables\.

These views provide quicker and easier access to commonly queried data found in those tables\.

System views with the prefix SVCS provide details about queries on both the main and Concurrency Scaling clusters\. The views are similar to the views with the prefix SVL except that the SVL views provide information only for queries run on the main cluster\. 

**Note**  
The SVL\_QUERY\_SUMMARY view only contains information about queries executed by Amazon Redshift, not other utility and DDL commands\. For a complete listing and information on all statements executed by Amazon Redshift, including DDL and utility commands, you can query the SVL\_STATEMENTTEXT view

**Topics**
+ [SVV\_COLUMNS](r_SVV_COLUMNS.md)
+ [SVL\_COMPILE](r_SVL_COMPILE.md)
+ [SVV\_DISKUSAGE](r_SVV_DISKUSAGE.md)
+ [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md)
+ [SVV\_EXTERNAL\_DATABASES](r_SVV_EXTERNAL_DATABASES.md)
+ [SVV\_EXTERNAL\_PARTITIONS](r_SVV_EXTERNAL_PARTITIONS.md)
+ [SVV\_EXTERNAL\_SCHEMAS](r_SVV_EXTERNAL_SCHEMAS.md)
+ [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md)
+ [SVV\_INTERLEAVED\_COLUMNS](r_SVV_INTERLEAVED_COLUMNS.md)
+ [SVL\_QERROR](r_SVL_QERROR.md)
+ [SVL\_QLOG](r_SVL_QLOG.md)
+ [SVV\_QUERY\_INFLIGHT](r_SVV_QUERY_INFLIGHT.md)
+ [SVL\_QUERY\_QUEUE\_INFO](r_SVL_QUERY_QUEUE_INFO.md)
+ [SVL\_QUERY\_METRICS](r_SVL_QUERY_METRICS.md)
+ [SVL\_QUERY\_METRICS\_SUMMARY](r_SVL_QUERY_METRICS_SUMMARY.md)
+ [SVL\_QUERY\_REPORT](r_SVL_QUERY_REPORT.md)
+ [SVV\_QUERY\_STATE](r_SVV_QUERY_STATE.md)
+ [SVL\_QUERY\_SUMMARY](r_SVL_QUERY_SUMMARY.md)
+ [SVL\_S3LOG](r_SVL_S3LOG.md)
+ [SVL\_S3PARTITION](r_SVL_S3PARTITION.md)
+ [SVL\_S3QUERY](r_SVL_S3QUERY.md)
+ [SVL\_S3QUERY\_SUMMARY](r_SVL_S3QUERY_SUMMARY.md)
+ [SVL\_S3RETRIES](r_SVL_S3RETRIES.md)
+ [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)
+ [SVV\_TABLES](r_SVV_TABLES.md)
+ [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md)
+ [SVV\_TRANSACTIONS](r_SVV_TRANSACTIONS.md)
+ [SVL\_USER\_INFO](r_SVL_USER_INFO.md)
+ [SVL\_UDF\_LOG](r_SVL_UDF_LOG.md)
+ [SVV\_VACUUM\_PROGRESS](r_SVV_VACUUM_PROGRESS.md)
+ [SVV\_VACUUM\_SUMMARY](r_SVV_VACUUM_SUMMARY.md)
+ [SVL\_VACUUM\_PERCENTAGE](r_SVL_VACUUM_PERCENTAGE.md)
+ [SVCS\_ALERT\_EVENT\_LOG](r_SVCS_ALERT_EVENT_LOG.md)
+ [SVCS\_COMPILE](r_SVCS_COMPILE.md)
+ [SVCS\_EXPLAIN](r_SVCS_EXPLAIN.md)
+ [SVCS\_PLAN\_INFO](r_SVCS_PLAN_INFO.md)
+ [SVCS\_QUERY\_SUMMARY](r_SVCS_QUERY_SUMMARY.md)
+ [SVCS\_STREAM\_SEGS](r_SVCS_STREAM_SEGS.md)
+ [SVCS\_CONCURRENCY\_SCALING\_USAGE](r_SVCS_CONCURRENCY_SCALING_USAGE.md)