# SVL\_AUTO\_WORKER\_ACTION<a name="r_SVL_AUTO_WORKER_ACTION"></a>

Records automated actions taken by Amazon Redshift on tables defined for automatic optimization\. 

SVL\_AUTO\_WORKER\_ACTION is visible to all users\.

## Table columns<a name="r_SVL_AUTO_WORKER_ACTION-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_AUTO_WORKER_ACTION.html)

Some examples of values of the `status` column are as follows: 
+ Skipped:Table not found\.
+ Skipped:Recommendation is empty\.
+ Skipped:Apply sortkey recommendation is disabled\.
+ Skipped:Retry exceeds the maximum limit for a table\.
+ Skipped:Table column has changed\.
+ Abort:This table is not AUTO\.
+ Abort:This table has been recently converted\.
+ Abort:This table exceeds table size threshold\.
+ Abort:This table is already the recommended style\.
+ Checkpoint: progress *21\.9963*%\.

## Sample queries<a name="r_SVL_AUTO_WORKER_ACTION-sample-queries"></a>

In the following example, the rows in the result show actions taken by Amazon Redshift\. 

```
select table_id, type, status, eventtime, sequence, previous_state
from SVL_AUTO_WORKER_ACTION;
```

```
 table_id |  type   |                        status                        |         eventtime          | sequence | previous_state
----------+---------+------------------------------------------------------+----------------------------+----------+----------------
   118082 | sortkey | Start                                                | 2020-08-22 19:42:20.727049 | 0        |
   118078 | sortkey | Start                                                | 2020-08-22 19:43:54.728819 | 0        |
   118082 | sortkey | Start                                                | 2020-08-22 19:42:52.690264 | 0        |
   118072 | sortkey | Start                                                | 2020-08-22 19:44:14.793572 | 0        |
   118082 | sortkey | Failed                                               | 2020-08-22 19:42:20.728917 | 0        |
   118078 | sortkey | Complete                                             | 2020-08-22 19:43:54.792705 | 0        | SORTKEY: None;
   118086 | sortkey | Complete                                             | 2020-08-22 19:42:00.72635  | 0        | SORTKEY: None;
   118082 | sortkey | Complete                                             | 2020-08-22 19:43:34.728144 | 0        | SORTKEY: None;
   118072 | sortkey | Skipped:Retry exceeds the maximum limit for a table. | 2020-08-22 19:44:46.706155 | 0        |
   118086 | sortkey | Start                                                | 2020-08-22 19:42:00.685255 | 0        |
   118082 | sortkey | Start                                                | 2020-08-22 19:43:34.69531  | 0        |
   118072 | sortkey | Start                                                | 2020-08-22 19:44:46.703331 | 0        |
   118082 | sortkey | Checkpoint: progress 14.755079%                      | 2020-08-22 19:42:52.692828 | 0        |
   118072 | sortkey | Failed                                               | 2020-08-22 19:44:14.796071 | 0        |  
   116723 | sortkey | Abort:This table is not AUTO.                        | 2020-10-28 05:12:58.479233 | 0        |
   110203 | distkey | Abort:This table is not AUTO.                        | 2020-10-28 05:45:54.67259  | 0        |
```