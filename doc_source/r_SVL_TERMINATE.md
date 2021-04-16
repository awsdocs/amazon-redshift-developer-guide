# SVL\_TERMINATE<a name="r_SVL_TERMINATE"></a>

Records the time when a user cancels or terminates a process\.

SELECT PG\_TERMINATE\_BACKEND\(*pid*\), SELECT PG\_CANCEL\_BACKEND\(*pid*\), and CANCEL *pid* creates a log entry in SVL\_TERMINATE\. 

SVL\_ TERMINATE is visible only to the superuser\.

## Table columns<a name="r_SVL_TERMINATE-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_TERMINATE.html)

The following command shows the latest cancelled query\.

```
select * from svl_terminate order by eventtime desc limit 1;
 pid  |         eventtime          | userid |  type  
------+----------------------------+--------+--------
 8324 | 2020-03-24 09:42:07.298937 |      1 | CANCEL
(1 row)
```