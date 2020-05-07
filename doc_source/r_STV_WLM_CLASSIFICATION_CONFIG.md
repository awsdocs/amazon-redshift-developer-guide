# STV\_WLM\_CLASSIFICATION\_CONFIG<a name="r_STV_WLM_CLASSIFICATION_CONFIG"></a>

Contains the current classification rules for WLM\. 

STV\_WLM\_CLASSIFICATION\_CONFIG is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_WLM_CLASSIFICATION_CONFIG-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_CLASSIFICATION_CONFIG.html)

## Sample query<a name="r_STV_WLM_CLASSIFICATION_CONFIG-sample-query2"></a>

```
select * from STV_WLM_CLASSIFICATION_CONFIG;

id | condition                                   | action_seq | action | action_service_class
---+---------------------------------------------+------------+--------+---------------------
 1 | (system user) and (query group: health)     |          0 | assign |                    1
 2 | (system user) and (query group: metrics)    |          0 | assign |                    2
 3 | (system user) and (query group: cmstats)    |          0 | assign |                    3
 4 | (system user)                               |          0 | assign |                    4
 5 | (super user) and (query group: superuser)   |          0 | assign |                    5
 6 | (query group: querygroup1)                  |          0 | assign |                    6
 7 | (user group: usergroup1)                    |          0 | assign |                    6
 8 | (user group: usergroup2)                    |          0 | assign |                    7
 9 | (query group: querygroup3)                  |          0 | assign |                    8
10 | (query group: querygroup4)                  |          0 | assign |                    9
11 | (user group: usergroup4)                    |          0 | assign |                    9
12 | (query group: querygroup*)                  |          0 | assign |                   10
13 | (user group: usergroup*)                    |          0 | assign |                   10
14 | (querytype: any)                            |          0 | assign |                   11
(4 rows)
```