# STL\_SESSIONS<a name="r_STL_SESSIONS"></a>

Returns information about user session history\.

STL\_SESSIONS differs from STV\_SESSIONS in that STL\_SESSIONS contains session history, where STV\_SESSIONS contains the current active sessions\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_SESSIONS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_SESSIONS.html)

## Sample queries<a name="r_STL_SESSIONS-sample-queries"></a>

To view session history for the TICKIT database, type the following query:

```
select starttime, process, user_name
from stl_sessions
where db_name='tickit' order by starttime;
```

This query returns the following sample output:

```
    starttime              | process |  user_name
---------------------------+---------+-------------
2008-09-15 09:54:06.746705 |   32358 | dwuser
2008-09-15 09:56:34.30275  |   32744 | dwuser
2008-09-15 11:20:34.694837 |   14906 | dwuser
2008-09-15 11:22:16.749818 |   15148 | dwuser
2008-09-15 14:32:44.66112  |   14031 | dwuser
2008-09-15 14:56:30.22161  |   18380 | dwuser
2008-09-15 15:28:32.509354 |   24344 | dwuser
2008-09-15 16:01:00.557326 |   30153 | dwuser
2008-09-15 17:28:21.419858 |   12805 | dwuser
2008-09-15 20:58:37.601937 |   14951 | dwuser
2008-09-16 11:12:30.960564 |   27437 | dwuser
2008-09-16 14:11:37.639092 |   23790 | dwuser
2008-09-16 15:13:46.02195  |    1355 | dwuser
2008-09-16 15:22:36.515106 |    2878 | dwuser
2008-09-16 15:44:39.194579 |    6470 | dwuser
2008-09-16 16:50:27.02138  |   17254 | dwuser
2008-09-17 12:05:02.157208 |    8439 | dwuser
(17 rows)
```