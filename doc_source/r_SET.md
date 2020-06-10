# SET<a name="r_SET"></a>

Sets the value of a server configuration parameter\. Use the SET command to override a setting for the duration of the current session or transaction only\.

Use the [RESET](r_RESET.md) command to return a parameter to its default value\. 

You can change the server configuration parameters in several ways\. For more information, see [Modifying the server configuration](t_Modifying_the_default_settings.md)\. 

## Syntax<a name="r_SET-synopsis"></a>

```
SET { [ SESSION | LOCAL ]
{ SEED | parameter_name } { TO | = }
{ value | 'value' | DEFAULT } |
SEED TO value }
```

## Parameters<a name="r_SET-parameters"></a>

SESSION   
Specifies that the setting is valid for the current session\. Default value\.

LOCAL   
Specifies that the setting is valid for the current transaction\. 

SEED TO *value*   
Sets an internal seed to be used by the RANDOM function for random number generation\.  
SET SEED takes a numeric *value* between 0 and 1, and multiplies this number by \(231\-1\) for use with the [RANDOM function](r_RANDOM.md) function\. If you use SET SEED before making multiple RANDOM calls, RANDOM generates numbers in a predictable sequence\.

 *parameter\_name*   
Name of the parameter to set\. See [Modifying the server configuration](t_Modifying_the_default_settings.md) for information about parameters\.

 *value*   
New parameter value\. Use single quotation marks to set the value to a specific string\. If using SET SEED, this parameter contains the SEED value\. 

DEFAULT   
Sets the parameter to the default value\.

## Examples<a name="r_SET-examples"></a>

 **Changing a parameter for the current session** 

The following example sets the datestyle:

```
set datestyle to 'SQL,DMY';
```

 **Setting a query group for workload management** 

If query groups are listed in a queue definition as part of the cluster's WLM configuration, you can set the QUERY\_GROUP parameter to a listed query group name\. Subsequent queries are assigned to the associated query queue\. The QUERY\_GROUP setting remains in effect for the duration of the session or until a RESET QUERY\_GROUP command is encountered\.

This example runs two queries as part of the query group 'priority', then resets the query group\. 

```
set query_group to 'priority';
select tbl, count(*)from stv_blocklist;
select query, elapsed, substring from svl_qlog order by query desc limit 5; 
reset query_group;
```

See [Implementing workload management](cm-c-implementing-workload-management.md) 

 **Setting a label for a group of queries** 

The QUERY\_GROUP parameter defines a label for one or more queries that are executed in the same session after a SET command\. In turn, this label is logged when queries are executed and can be used to constrain results returned from the STL\_QUERY and STV\_INFLIGHT system tables and the SVL\_QLOG view\. 

```
show query_group;
query_group
-------------
unset
(1 row)

set query_group to '6 p.m.';


show query_group;
query_group
-------------
6 p.m.
(1 row)

select * from sales where salesid=500;
salesid | listid | sellerid | buyerid | eventid | dateid | ...
---------+--------+----------+---------+---------+--------+-----
500 |    504 |     3858 |    2123 |    5871 |   2052 | ...
(1 row)

reset query_group;

select query, trim(label) querygroup, pid, trim(querytxt) sql
from stl_query
where label ='6 p.m.';
query | querygroup |  pid  |                  sql
-------+------------+-------+----------------------------------------
57 | 6 p.m.     | 30711 | select * from sales where salesid=500;
(1 row)
```

Query group labels are a useful mechanism for isolating individual queries or groups of queries that are run as part of scripts\. You don't need to identify and track queries by their IDs; you can track them by their labels\.

 **Setting a seed value for random number generation** 

The following example uses the SEED option with SET to cause the RANDOM function to generate numbers in a predictable sequence\.

First, return three RANDOM integers without setting the SEED value first: 

```
select cast (random() * 100 as int);
int4
------
6
(1 row)

select cast (random() * 100 as int);
int4
------
68
(1 row)

select cast (random() * 100 as int);
int4
------
56
(1 row)
```

Now, set the SEED value to `.25`, and return three more RANDOM numbers: 

```
set seed to .25;

select cast (random() * 100 as int);
int4
------
21
(1 row)

select cast (random() * 100 as int);
int4
------
79
(1 row)

select cast (random() * 100 as int);
int4
------
12
(1 row)
```

Finally, reset the SEED value to `.25`, and verify that RANDOM returns the same results as the previous three calls: 

```
set seed to .25;

select cast (random() * 100 as int);
int4
------
21
(1 row)

select cast (random() * 100 as int);
int4
------
79
(1 row)

select cast (random() * 100 as int);
int4
------
12
(1 row)
```