# query\_group<a name="r_query_group"></a>

## Values \(default in bold\)<a name="r_query_group-values"></a>

No default; the value can be any character string\. 

## Description<a name="r_query_group-description"></a>

This parameter applies a user\-defined label to a group of queries that are run during the same session\. This label is captured in the query logs and can be used to constrain results from the STL\_QUERY and STV\_INFLIGHT tables and the SVL\_QLOG view\. For example, you can apply a separate label to every query that you run to uniquely identify queries without having to look up their IDs\. 

This parameter does not exist in the server configuration file and must be set at runtime with a SET command\. Although you can use a long character string as a label, the label is truncated to 30 characters in the LABEL column of the STL\_QUERY table and the SVL\_QLOG view \(and to 15 characters in STV\_INFLIGHT\)\. 

In the following example, query\_group is set to **Monday**, then several queries are executed with that label: 

```
set query_group to 'Monday';
SET
select * from category limit 1;
...
...
select query, pid, substring, elapsed, label
from svl_qlog where label ='Monday'
order by query;

query | pid  |        substring                   |  elapsed  | label
------+------+------------------------------------+-----------+--------
789   | 6084 | select * from category limit 1;    | 65468     | Monday
790   | 6084 | select query, trim(label) from ... | 1260327   | Monday
791   | 6084 | select * from svl_qlog where ..    | 2293547   | Monday
792   | 6084 | select count(*) from bigsales;     | 108235617 | Monday
...
```