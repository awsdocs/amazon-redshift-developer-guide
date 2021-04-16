# Section 4: Using wlm\_query\_slot\_count to temporarily override the concurrency level in a queue<a name="tutorial-wlm-query-slot-count"></a>

Sometimes, users might temporarily need more resources for a particular query\. If so, they can use the wlm\_query\_slot\_count configuration setting to temporarily override the way slots are allocated in a query queue\. *Slots* are units of memory and CPU that are used to process queries\. You might override the slot count when you have occasional queries that take a lot of resources in the cluster, such as when you perform a VACUUM operation in the database\. 

You might find that users often need to set wlm\_query\_slot\_count for certain types of queries\. If so, consider adjusting the WLM configuration and giving users a queue that better suits the needs of their queries\. For more information about temporarily overriding the concurrency level by using slot count, see [wlm\_query\_slot\_count](r_wlm_query_slot_count.md)\.

## Step 1: Override the concurrency level using wlm\_query\_slot\_count<a name="tutorial-wlm-override-slot-count"></a>

For the purposes of this tutorial, we run the same long\-running SELECT query\. We run it as the `adminwlm` user using wlm\_query\_slot\_count to increase the number of slots available for the query\.

### To override the concurrency level using wlm\_query\_slot\_count<a name="how-to-wlm-override-slot-count"></a>

1. Increase the limit on the query to make sure that you have enough time to query the WLM\_QUERY\_STATE\_VW view and see a result\. 

   ```
   set wlm_query_slot_count to 3; 
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. Now, query WLM\_QUERY\_STATE\_VW use the masteruser account to see how the query is running\.

   ```
   select * from wlm_query_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_170.png)

   Notice that the slot count for the query is 3\. This count means that the query is using all three slots to process the query, allocating all of the resources in the queue to that query\.

1. Now, run the following query\.

   ```
   select * from WLM_QUEUE_STATE_VW;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_160.png)

   The wlm\_query\_slot\_count configuration setting is valid for the current session only\. If that session expires, or another user runs a query, the WLM configuration is used\.

1. Reset the slot count and rerun the test\.

   ```
   reset wlm_query_slot_count;
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

   The following are example results\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_180.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_190.png)

## Step 2: Run queries from different sessions<a name="tutorial-wlm-run-queries-from-different-sessions"></a>

Next, run queries from different sessions\.

### To run queries from different sessions<a name="how-to-wlm-run-queries-from-different-sessions"></a>

1. In psql window 1 and 2, run the following to use the test query group\.

   ```
   set query_group to test;
   ```

1. In psql window 1, run the following long\-running query\.

   ```
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. As the long\-running query is still going in psql window 1, run the following\. These commands increase the slot count to use all the slots for the queue and then start running the long\-running query\.

   ```
   set wlm_query_slot_count to 2;
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. Open a third psql window and query the views to see the results\.

   ```
   select * from wlm_queue_state_vw;
   select * from wlm_query_state_vw;
   ```

   The following are example results\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_200.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_210.png)

   Notice that the first query is using one of the slots allocated to queue 1 to run the query\. In addition, notice that there is one query that is waiting in the queue \(where `queued` is `1` and `state` is `QueuedWaiting`\)\. After the first query completes, the second one begins running\. This execution happens because both queries are routed to the `test` query group, and the second query must wait for enough slots to begin processing\.