# Section 1: Understanding the default queue processing behavior<a name="tutorial-wlm-understanding-default-processing"></a>

Before you start to configure manual WLM, it’s useful to understand the default behavior of queue processing in Amazon Redshift\. In this section, you create two database views that return information from several system tables\. Then you run some test queries to see how queries are routed by default\. For more information about system tables, see [System tables reference](cm_chap_system-tables.md)\. 

## Step 1: Create the WLM\_QUEUE\_STATE\_VW view<a name="tutorial-wlm-create-queue-state-view"></a>

In this step, you create a view called WLM\_QUEUE\_STATE\_VW\. This view returns information from the following system tables\.
+ [STV\_WLM\_CLASSIFICATION\_CONFIG](r_STV_WLM_CLASSIFICATION_CONFIG.md)
+ [STV\_WLM\_SERVICE\_CLASS\_CONFIG](r_STV_WLM_SERVICE_CLASS_CONFIG.md)
+ [STV\_WLM\_SERVICE\_CLASS\_STATE](r_STV_WLM_SERVICE_CLASS_STATE.md)

You use this view throughout the tutorial to monitor what happens to queues after you change the WLM configuration\. The following table describes the data that the WLM\_QUEUE\_STATE\_VW view returns\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-wlm-understanding-default-processing.html)

### To create the WLM\_QUEUE\_STATE\_VW view<a name="how-to-wlm-create-queue-state-view"></a>

1. Open psql and connect to your TICKIT sample database\. If you do not have this database, see [Prerequisites](tutorial-configuring-workload-management.md#tutorial-wlm-prereq)\.

1. Run the following query to create the WLM\_QUEUE\_STATE\_VW view\.

   ```
   create view WLM_QUEUE_STATE_VW as
   select (config.service_class-5) as queue
   , trim (class.condition) as description
   , config.num_query_tasks as slots
   , config.query_working_mem as mem
   , config.max_execution_time as max_time
   , config.user_group_wild_card as "user_*"
   , config.query_group_wild_card as "query_*"
   , state.num_queued_queries queued
   , state.num_executing_queries executing
   , state.num_executed_queries executed
   from
   STV_WLM_CLASSIFICATION_CONFIG class,
   STV_WLM_SERVICE_CLASS_CONFIG config,
   STV_WLM_SERVICE_CLASS_STATE state
   where
   class.action_service_class = config.service_class 
   and class.action_service_class = state.service_class 
   and config.service_class > 4
   order by config.service_class;
   ```

1. Run the following query to see the information that the view contains\.

   ```
   select * from wlm_queue_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_010.png)

## Step 2: Create the WLM\_QUERY\_STATE\_VW view<a name="tutorial-wlm-create-query-state-view"></a>

In this step, you create a view called WLM\_QUERY\_STATE\_VW\. This view returns information from the [STV\_WLM\_QUERY\_STATE](r_STV_WLM_QUERY_STATE.md) system table\.

You use this view throughout the tutorial to monitor the queries that are running\. The following table describes the data that the WLM\_QUERY\_STATE\_VW view returns\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-wlm-understanding-default-processing.html)

### To create the WLM\_QUERY\_STATE\_VW view<a name="how-to-wlm-create-query-state-view"></a>

1. In psql, run the following query to create the WLM\_QUERY\_STATE\_VW view\.

   ```
   create view WLM_QUERY_STATE_VW as
   select query, (service_class-5) as queue, slot_count, trim(wlm_start_time) as start_time, trim(state) as state, trim(queue_time) as queue_time, trim(exec_time) as exec_time
   from stv_wlm_query_state;
   ```

1. Run the following query to see the information that the view contains\.

   ```
   select * from wlm_query_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_020.png)

## Step 3: Run test queries<a name="tutorial-wlm-run-test-queries"></a>

In this step, you run queries from multiple connections in psql and review the system tables to determine how the queries were routed for processing\. 

For this step, you need two psql windows open: 
+ In psql window 1, you run queries that monitor the state of the queues and queries using the views you already created in this tutorial\.
+ In psql window 2, you run long\-running queries to change the results you find in psql window 1\.

### To run the test queries<a name="how-to-wlm-run-test-queries"></a>

1. Open two psql windows\. If you already have one window open, you only need to open a second window\. You can use the same user account for both of these connections\.

1. In psql window 1, run the following query\.

   ```
   select * from wlm_query_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_030.png)

   This query returns a self\-referential result\. The query that is currently executing is the SELECT statement from this view\. A query on this view always returns at least one result\. Compare this result with the result that occurs after starting the long\-running query in the next step\.

1. In psql window 2, run a query from the TICKIT sample database\. This query should run for approximately a minute so that you have time to explore the results of the WLM\_QUEUE\_STATE\_VW view and the WLM\_QUERY\_STATE\_VW view that you created earlier\. In some cases, you might find that the query doesn't run long enough for you to query both views\. In these cases, you can increase the value of the filter on `l.listid `to make it run longer\.
**Note**  
To reduce query execution time and improve system performance, Amazon Redshift caches the results of certain types of queries in memory on the leader node\. When result caching is enabled, subsequent queries run much faster\. To prevent the query from running to quickly, disable result caching for the current session\.

   To disable result caching for the current session, set the [enable\_result\_cache\_for\_session](r_enable_result_cache_for_session.md) parameter to `off`, as shown following\.

   ```
   set enable_result_cache_for_session to off;
   ```

   In psql window 2, run the following query\.

   ```
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid < 100000;
   ```

1. In psql window 1, query WLM\_QUEUE\_STATE\_VW and WLM\_QUERY\_STATE\_VW and compare the results to your earlier results\.

   ```
   select * from wlm_queue_state_vw;
   select * from wlm_query_state_vw;
   ```

   The following are example results\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_040.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_050.png)

Note the following differences between your previous queries and the results in this step:
+ There are two rows now in WLM\_QUERY\_STATE\_VW\. One result is the self\-referential query for running a SELECT operation on this view\. The second result is the long\-running query from the previous step\.
+ The executing column in WLM\_QUEUE\_STATE\_VW has increased from 1 to 2\. This column entry means that there are two queries running in the queue\.
+ The executed column is incremented each time you run a query in the queue\.

The WLM\_QUEUE\_STATE\_VW view is useful for getting an overall view of the queues and how many queries are being processed in each queue\. The WLM\_QUERY\_STATE\_VW view is useful for getting a more detailed view of the individual queries that are currently running\.