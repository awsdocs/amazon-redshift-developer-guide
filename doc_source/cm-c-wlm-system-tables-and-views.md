# WLM system tables and views<a name="cm-c-wlm-system-tables-and-views"></a>

WLM configures query queues according to WLM service classes, which are internally defined\. Amazon Redshift creates several internal queues according to these service classes along with the queues defined in the WLM configuration\. The terms *queue* and *service class* are often used interchangeably in the system tables\. The superuser queue uses service class 5\. User\-defined queues use service class 6 and greater\.

You can view the status of queries, queues, and service classes by using WLM\-specific system tables\. Query the following system tables to do the following:
+ View which queries are being tracked and what resources are allocated by the workload manager\.
+ See which queue a query has been assigned to\.
+ View the status of a query that is currently being tracked by the workload manager\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-system-tables-and-views.html)

 You use the task ID to track a query in the system tables\. The following example shows how to obtain the task ID of the most recently submitted user query: 

```
select task from stl_wlm_query where exec_start_time =(select max(exec_start_time) from stl_wlm_query); 

task 
------ 
137 
(1 row)
```

 The following example displays queries that are currently executing or waiting in various service classes \(queues\)\. This query is useful in tracking the overall concurrent workload for Amazon Redshift: 

```
select * from stv_wlm_query_state order by query;


xid |task|query|service_| wlm_start_  |  state  |queue_ | exec_
    |    |     |class   | time        |         |time   | time
----+----+-----+--------+-------------+---------+-------+--------
2645| 84 | 98  | 3      | 2010-10-... |Returning|   0   | 3438369
2650| 85 | 100 | 3      | 2010-10-... |Waiting  |   0   | 1645879
2660| 87 | 101 | 2      | 2010-10-... |Executing|   0   | 916046
2661| 88 | 102 | 1      | 2010-10-... |Executing|   0   | 13291
(4 rows)
```

## WLM service class IDs<a name="wlm-service-class-ids"></a>

The following table lists the IDs assigned to service classes\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/cm-c-wlm-system-tables-and-views.html)