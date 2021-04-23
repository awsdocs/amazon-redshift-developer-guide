# Working with views in data sharing<a name="datashare-views"></a>

A producer cluster can share regular, late\-binding, and materialized views\. When sharing regular or late\-binding views, you don't have to share the base tables\. The following table shows how views are supported with data sharing\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/datashare-views.html)

The following query shows the output of a regular view that is supported with data sharing\. For information about regular view definition, see [CREATE VIEW](r_CREATE_VIEW.md)\.

```
SELECT * FROM tickit_db.public.myevent_regular_vw 
ORDER BY eventid LIMIT 5;

   eventid  |  eventname
  ----------+-------------
     3835   | LeAnn Rimes
     3967   | LeAnn Rimes
     4856   | LeAnn Rimes
     4948   | LeAnn Rimes     
     5131   | LeAnn Rimes
```

The following query shows the output of a late binding view that is supported with data sharing\. For information about late binding view definition, see [CREATE VIEW](r_CREATE_VIEW.md)\.

```
SELECT * FROM tickit_db.public.event_lbv 
ORDER BY eventid LIMIT 5;
         
 eventid | venueid | catid | dateid |          eventname           |      starttime
 --------+---------+-------+--------+------------------------------+---------------------
     1   |   305   |   8   |  1851  |        Gotterdammerung       | 2008-01-25 14:30:00
     2   |   306   |   8   |  2114  |           Boris Godunov      | 2008-10-15 20:00:00
     3   |   302   |   8   |  1935  |              Salome          | 2008-04-19 14:30:00
     4   |   309   |   8   |  2090  |  La Cenerentola (Cinderella) | 2008-09-21 14:30:00
     5   |   302   |   8   |  1982  |          Il Trovatore        | 2008-06-05 19:00:00
```

The following query shows the output of a materialized view that is supported with data sharing\. For information about materialized view definition, see [CREATE MATERIALIZED VIEW](materialized-view-create-sql-command.md)\.

```
SELECT * FROM tickit_db.public.tickets_mv;

  catgroup | qtysold
 ----------+---------
  Concerts | 195444
    Shows  | 149905
```

You can maintain common tables across all tenants in a producer cluster\. You can also share subsets of data filtered by dimension columns, such as `tenant_id` \(`account_id` or `namespace_id`\), to consumer clusters\. To do this, you can define a view on the base table with a filter on these ID columns, for example `current_aws_account = tenant_id`\. On the consumer side, when you query the view, you see only the rows that qualify for your account\. To do this, you can use the Amazon Redshift context functions `current_aws_account` and `current_namespace`\.

The following query returns the account ID in which the current Amazon Redshift cluster resides\. You can run this query if you are connected to Amazon Redshift\.

```
select current_user, current_aws_account;

current_user | current_aws_account
-------------+--------------------
dwuser       |    111111111111
(1row)
```

The following query returns the namespace of the current Amazon Redshift cluster\. You can run this query if you are connected to the database\.

```
select current_user, current_namespace; 

current_user | current_namespace
-------------+--------------------------------------
dwuser       | 86b5169f-01dc-4a6f-9fbb-e2e24359e9a8
(1 row)
```