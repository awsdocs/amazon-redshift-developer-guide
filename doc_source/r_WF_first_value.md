# FIRST\_VALUE and LAST\_VALUE window functions<a name="r_WF_first_value"></a>

 Given an ordered set of rows, FIRST\_VALUE returns the value of the specified expression with respect to the first row in the window frame\. The LAST\_VALUE function returns the value of the expression with respect to the last row in the frame\. 

## Syntax<a name="r_WF_first_value-synopsis"></a>

```
FIRST_VALUE | LAST_VALUE
( expression [ IGNORE NULLS | RESPECT NULLS ] ) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list frame_clause ]
)
```

## Arguments<a name="r_WF_first_value-arguments"></a>

 *expression*   
 The target column or expression that the function operates on\. 

IGNORE NULLS   
When this option is used with FIRST\_VALUE, the function returns the first value in the frame that is not NULL \(or NULL if all values are NULL\)\. When this option is used with LAST\_VALUE, the function returns the last value in the frame that is not NULL \(or NULL if all values are NULL\)\. 

RESPECT NULLS   
 Indicates that Amazon Redshift should include null values in the determination of which row to use\. RESPECT NULLS is supported by default if you do not specify IGNORE NULLS\. 

OVER   
Introduces the window clauses for the function\. 

PARTITION BY *expr\_list*   
Defines the window for the function in terms of one or more expressions\. 

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY clause is specified, ORDER BY sorts the entire table\. If you specify an ORDER BY clause, you must also specify a *frame\_clause*\.   
The results of the FIRST\_VALUE and LAST\_VALUE functions depend on the ordering of the data\. The results are nondeterministic in the following cases:   
+ When no ORDER BY clause is specified and a partition contains two different values for an expression 
+ When the expression evaluates to different values that correspond to the same value in the ORDER BY list\. 

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows in the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window function syntax summary](r_Window_function_synopsis.md)\. 

## Data types<a name="c_Supported_data_types_wf_first_value"></a>

These functions support expressions that use any of the Amazon Redshift data types\. The return type is the same as the type of the *expression*\.

## Examples<a name="r_WF_first_value-examples"></a>

The following example returns the seating capacity for each venue in the VENUE table, with the results ordered by capacity \(high to low\)\. The FIRST\_VALUE function is used to select the name of the venue that corresponds to the first row in the frame: in this case, the row with the highest number of seats\. The results are partitioned by state, so when the VENUESTATE value changes, a new first value is selected\. The window frame is unbounded so the same first value is selected for each row in each partition\. 

For California, `Qualcomm Stadium` has the highest number of seats \(`70561`\), so this name is the first value for all of the rows in the `CA` partition\. 

```
select venuestate, venueseats, venuename,
first_value(venuename)
over(partition by venuestate
order by venueseats desc
rows between unbounded preceding and unbounded following)
from (select * from venue where venueseats >0)
order by venuestate;

venuestate | venueseats |           venuename            |         first_value
-----------+------------+--------------------------------+------------------------------
CA         |      70561 | Qualcomm Stadium               | Qualcomm Stadium
CA         |      69843 | Monster Park                   | Qualcomm Stadium
CA         |      63026 | McAfee Coliseum                | Qualcomm Stadium
CA         |      56000 | Dodger Stadium                 | Qualcomm Stadium
CA         |      45050 | Angel Stadium of Anaheim       | Qualcomm Stadium
CA         |      42445 | PETCO Park                     | Qualcomm Stadium
CA         |      41503 | AT&T Park                      | Qualcomm Stadium
CA         |      22000 | Shoreline Amphitheatre         | Qualcomm Stadium
CO         |      76125 | INVESCO Field                  | INVESCO Field
CO         |      50445 | Coors Field                    | INVESCO Field
DC         |      41888 | Nationals Park                 | Nationals Park
FL         |      74916 | Dolphin Stadium                | Dolphin Stadium
FL         |      73800 | Jacksonville Municipal Stadium | Dolphin Stadium
FL         |      65647 | Raymond James Stadium          | Dolphin Stadium
FL         |      36048 | Tropicana Field                | Dolphin Stadium
...
```

The next example uses the LAST\_VALUE function instead of FIRST\_VALUE; otherwise, the query is the same as the previous example\. For California, `Shoreline Amphitheatre` is returned for every row in the partition because it has the lowest number of seats \(`22000`\)\. 

```
select venuestate, venueseats, venuename,
last_value(venuename)
over(partition by venuestate
order by venueseats desc
rows between unbounded preceding and unbounded following)
from (select * from venue where venueseats >0)
order by venuestate;

venuestate | venueseats |           venuename            |          last_value
-----------+------------+--------------------------------+------------------------------
CA         |      70561 | Qualcomm Stadium               | Shoreline Amphitheatre
CA         |      69843 | Monster Park                   | Shoreline Amphitheatre
CA         |      63026 | McAfee Coliseum                | Shoreline Amphitheatre
CA         |      56000 | Dodger Stadium                 | Shoreline Amphitheatre
CA         |      45050 | Angel Stadium of Anaheim       | Shoreline Amphitheatre
CA         |      42445 | PETCO Park                     | Shoreline Amphitheatre
CA         |      41503 | AT&T Park                      | Shoreline Amphitheatre
CA         |      22000 | Shoreline Amphitheatre         | Shoreline Amphitheatre
CO         |      76125 | INVESCO Field                  | Coors Field
CO         |      50445 | Coors Field                    | Coors Field
DC         |      41888 | Nationals Park                 | Nationals Park
FL         |      74916 | Dolphin Stadium                | Tropicana Field
FL         |      73800 | Jacksonville Municipal Stadium | Tropicana Field
FL         |      65647 | Raymond James Stadium          | Tropicana Field
FL         |      36048 | Tropicana Field                | Tropicana Field
...
```

The following example shows the use of the IGNORE NULLS option and relies on the addition of a new row to the VENUE table: 

```
insert into venue values(2000,null,'Stanford','CA',90000);
```

This new row contains a NULL value for the VENUENAME column\. Now repeat the FIRST\_VALUE query that was shown earlier in this section: 

```
select venuestate, venueseats, venuename,
first_value(venuename)
over(partition by venuestate
order by venueseats desc
rows between unbounded preceding and unbounded following)
from (select * from venue where venueseats >0)
order by venuestate;

venuestate | venueseats |         venuename          | first_value
-----------+------------+----------------------------+-------------
CA         |      90000 |                            |
CA         |      70561 | Qualcomm Stadium           |
CA         |      69843 | Monster Park               |
...
```

Because the new row contains the highest VENUESEATS value \(`90000`\) and its VENUENAME is NULL, the FIRST\_VALUE function returns NULL for the `CA` partition\. To ignore rows like this in the function evaluation, add the IGNORE NULLS option to the function argument: 

```
select venuestate, venueseats, venuename,
first_value(venuename ignore nulls)
over(partition by venuestate
order by venueseats desc
rows between unbounded preceding and unbounded following)
from (select * from venue where venuestate='CA')
order by venuestate;

venuestate | venueseats |         venuename          |   first_value
------------+------------+----------------------------+------------------
CA         |      90000 |                            | Qualcomm Stadium
CA         |      70561 | Qualcomm Stadium           | Qualcomm Stadium
CA         |      69843 | Monster Park               | Qualcomm Stadium
...
```