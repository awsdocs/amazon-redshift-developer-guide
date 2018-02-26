# STL\_CONNECTION\_LOG<a name="r_STL_CONNECTION_LOG"></a>

Logs authentication attempts and connections and disconnections\.

This table is visible only to superusers\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_CONNECTION_LOG-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_CONNECTION_LOG.html)

## Sample Queries<a name="r_STL_CONNECTION_LOG-sample-queries2"></a>

To view the details for open connections, execute the following query\.

```
select recordtime, username, dbname, remotehost, remoteport
from stl_connection_log
where event = 'initiating session'
and pid not in 
(select pid from stl_connection_log
where event = 'disconnecting session')
order by 1 desc;

recordtime          | username    | dbname     | remotehost    | remoteport                      
--------------------+-------------+------------+---------------+---------------------------------
2014-11-06 20:30:06 | rdsdb       | dev        | [local]       |                            
2014-11-06 20:29:37 | test001     | test       | 10.49.42.138  | 11111                           
2014-11-05 20:30:29 | rdsdb       | dev        | 10.49.42.138  | 33333                                                 
2014-11-05 20:28:35 | rdsdb       | dev        | [local]       |  
(4 rows)
```

The following example reflects a failed authentication attempt and a successful connection and disconnection\. 

```
select event, recordtime, remotehost, username
from stl_connection_log order by recordtime;            

            event      |         recordtime        |  remotehost  | username                      
-----------------------+---------------------------+--------------+---------
authentication failure | 2012-10-25 14:41:56.96391 | 10.49.42.138 | john                                              
authenticated          | 2012-10-25 14:42:10.87613 | 10.49.42.138 | john                                              
initiating session     | 2012-10-25 14:42:10.87638 | 10.49.42.138 | john                                              
disconnecting session  | 2012-10-25 14:42:19.95992 | 10.49.42.138 | john                                              
(4 rows)
```