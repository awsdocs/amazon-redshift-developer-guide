# Section 3: Routing queries to queues based on user groups and query groups<a name="tutorial-wlm-routing-queries-to-queues"></a>

Now you have your cluster associated with a new parameter group and you've configured WLM\. Next, run some queries to see how Amazon Redshift routes queries into queues for processing\.

## Step 1: View query queue configuration in the database<a name="tutorial-wlm-view-query-config"></a>

First, verify that the database has the WLM configuration that you expect\.

### To view the query queue configuration<a name="how-to-wlm-view-query-config"></a>

1. Open psql and run the following query\. The query uses the WLM\_QUEUE\_STATE\_VW view you created in [Step 1: Create the WLM\_QUEUE\_STATE\_VW view](tutorial-wlm-understanding-default-processing.md#tutorial-wlm-create-queue-state-view)\. If you already had a session connected to the database prior to the cluster reboot, you need to reconnect\.

   ```
   select * from wlm_queue_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_060.png)

   Compare these results to the results you received in [Step 1: Create the WLM\_QUEUE\_STATE\_VW view](tutorial-wlm-understanding-default-processing.md#tutorial-wlm-create-queue-state-view)\. Notice that there are now two additional queues\. Queue 1 is now the queue for the test query group, and queue 2 is the queue for the admin user group\.

   Queue 3 is now the default queue\. The last queue in the list is always the default queue\. That's the queue to which queries are routed by default if no user group or query group is specified in a query\.

1. Run the following query to confirm that your query now runs in queue 3\.

   ```
   select * from wlm_query_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_070.png)

## Step 2: Run a query using the query group queue<a name="tutorial-wlm-query-group"></a>

### To run a query using the query group queue<a name="how-to-wlm-query-group"></a>

1. Run the following query to route it to the `test` query group\.

   ```
   set query_group to test;
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. From the other psql window, run the following query\.

   ```
   select * from wlm_query_state_vw;
   ```

   The following is an example result\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_080.png)

   The query was routed to the test query group, which is queue 1 now\.

1. Select all from the queue state view\.

   ```
   select * from wlm_queue_state_vw;
   ```

   You see a result similar to the following\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_090.png)

1. Now, reset the query group and run the long query again:

   ```
   reset query_group;
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. Run the queries against the views to see the results\.

   ```
   select * from wlm_queue_state_vw;
   select * from wlm_query_state_vw;
   ```

   The following are example results\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_100.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_110.png)

   The result should be that the query is now running in queue 3 again\.

## Step 3: Create a database user and group<a name="tutorial-wlm-create-db-user-and-group"></a>

In [Step 1: Create a parameter group](tutorial-wlm-modifying-wlm-configuration.md#tutorial-wlm-create-parameter-group), you configured one of your query queues with a user group named `admin`\. Before you can run any queries in this queue, you need to create the user group in the database and add a user to the group\. Then you log on with psql using the new user’s credentials and run queries\. You need to run queries as a superuser, such as the masteruser, to create database users\.

### To create a new database user and user group<a name="how-to-wlm-create-db-user-and-group"></a>

1. In the database, create a new database user named `adminwlm` by running the following command in a psql window\.

   ```
   create user adminwlm createuser password '123Admin';
   ```

1. Then, run the following commands to create the new user group and add your new `adminwlm` user to it\.

   ```
   create group admin;
   alter group admin add user adminwlm;
   ```

## Step 4: Run a query using the user group queue<a name="tutorial-wlm-user-group-query"></a>

Next you run a query and route it to the user group queue\. You do this when you want to route your query to a queue that is configured to handle the type of query you want to run\.

### To run a query using the user group queue<a name="how-to-wlm-user-group-query"></a>

1. In psql window 2, run the following queries to switch to the `adminwlm` account and run a query as that user\.

   ```
   set session authorization 'adminwlm';
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. In psql window 1, run the following query to see the query queue that the queries are routed to\.

   ```
   select * from wlm_query_state_vw;
   select * from wlm_queue_state_vw;
   ```

   The following are example results\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_120.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_130.png)

   The queue that this query ran in is queue 2, the `admin` user queue\. Anytime you run queries logged in as this user, they run in queue 2 unless you specify a different query group to use\.  The chosen queue depends on the queue assignment rules\. For more information, see [WLM queue assignment rules](cm-c-wlm-queue-assignment-rules.md)\. 

1. Now run the following query from psql window 2\.

   ```
   set query_group to test;
   select avg(l.priceperticket*s.qtysold) from listing l, sales s where l.listid <40000;
   ```

1. In psql window 1, run the following query to see the query queue that the queries are routed to\.

   ```
   select * from wlm_queue_state_vw;
   select * from wlm_query_state_vw;
   ```

   The following are example results\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_140.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/psql_tutorial_wlm_150.png)

1. When you’re done, reset the query group\.

   ```
   reset query_group;
   ```