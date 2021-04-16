# Window functions<a name="c_Window_functions"></a>

By using window functions, you can enable your users to create analytic business queries more efficiently\. Window functions operate on a partition or "window" of a result set, and return a value for every row in that window\. In contrast, nonwindowed functions perform their calculations with respect to every row in the result set\. Unlike group functions that aggregate result rows, all rows in the table expression are retained\. 

 The values returned are calculated by using values from the sets of rows in that window\. For each row in the table, the window defines a set of rows that is used to compute additional attributes\. A window is defined using a window specification \(the OVER clause\), and is based on three main concepts: 
+  *Window partitioning,* which forms groups of rows \(PARTITION clause\) 
+  *Window ordering*, which defines an order or sequence of rows within each partition \(ORDER BY clause\) 
+  *Window frames*, which are defined relative to each row to further restrict the set of rows \(ROWS specification\) 

Window functions are the last set of operations performed in a query except for the final ORDER BY clause\. All joins and all WHERE, GROUP BY, and HAVING clauses are completed before the window functions are processed\. Therefore, window functions can appear only in the select list or ORDER BY clause\. You can use multiple window functions within a single query with different frame clauses\. You can also use window functions in other scalar expressions, such as CASE\. 

Amazon Redshift supports two types of window functions: aggregate and ranking\.

These are the supported aggregate functions: 
+ AVG 
+ COUNT 
+ CUME\_DIST
+ FIRST\_VALUE 
+ LAG 
+ LAST\_VALUE 
+ LEAD 
+ MAX 
+ MEDIAN 
+ MIN 
+ NTH\_VALUE 
+ PERCENTILE\_CONT
+ PERCENTILE\_DISC
+ RATIO\_TO\_REPORT
+ STDDEV\_POP 
+ STDDEV\_SAMP \(synonym for STDDEV\) 
+ SUM 
+ VAR\_POP 
+ VAR\_SAMP \(synonym for VARIANCE\) 

 These are the supported ranking functions: 
+ DENSE\_RANK 
+ NTILE 
+ PERCENT\_RANK
+ RANK 
+ ROW\_NUMBER

**Topics**
+ [Window function syntax summary](r_Window_function_synopsis.md)
+ [Unique ordering of data for window functions](r_Examples_order_by_WF.md)
+ [Overview example for window functions](#r_Window_function_example)
+ [AVG window function](r_WF_AVG.md)
+ [COUNT window function](r_WF_COUNT.md)
+ [CUME\_DIST window function](r_WF_CUME_DIST.md)
+ [DENSE\_RANK window function](r_WF_DENSE_RANK.md)
+ [FIRST\_VALUE and LAST\_VALUE window functions](r_WF_first_value.md)
+ [LAG window function](r_WF_LAG.md)
+ [LEAD window function](r_WF_LEAD.md)
+ [LISTAGG window function](r_WF_LISTAGG.md)
+ [MAX window function](r_WF_MAX.md)
+ [MEDIAN window function](r_WF_MEDIAN.md)
+ [MIN window function](r_WF_MIN.md)
+ [NTH\_VALUE window function](r_WF_NTH.md)
+ [NTILE window function](r_WF_NTILE.md)
+ [PERCENT\_RANK window function](r_WF_PERCENT_RANK.md)
+ [PERCENTILE\_CONT window function](r_WF_PERCENTILE_CONT.md)
+ [PERCENTILE\_DISC window function](r_WF_PERCENTILE_DISC.md)
+ [RANK window function](r_WF_RANK.md)
+ [RATIO\_TO\_REPORT window function](r_WF_RATIO_TO_REPORT.md)
+ [ROW\_NUMBER window function](r_WF_ROW_NUMBER.md)
+ [STDDEV\_SAMP and STDDEV\_POP window functions](r_WF_STDDEV.md)
+ [SUM window function](r_WF_SUM.md)
+ [VAR\_SAMP and VAR\_POP window functions](r_WF_VARIANCE.md)

## Overview example for window functions<a name="r_Window_function_example"></a>

Following, you can find an overview example demonstrating how to work with the window functions\. You can also find specific code examples with each function description\.

Some of the window function examples use a table named WINSALES, which contains 11 rows, as shown following\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_Window_functions.html)

The following script creates and populates the sample WINSALES table\.

```
create table winsales(
salesid int,
dateid date,
sellerid int,
buyerid char(10),
qty int,
qty_shipped int);

insert into winsales values
(30001, '8/2/2003', 3, 'b', 10, 10),
(10001, '12/24/2003', 1, 'c', 10, 10),
(10005, '12/24/2003', 1, 'a', 30, null),	
(40001, '1/9/2004', 4, 'a', 40, null),	
(10006, '1/18/2004', 1, 'c', 10, null),	
(20001, '2/12/2004', 2, 'b', 20, 20),
(40005, '2/12/2004', 4, 'a', 10, 10),
(20002, '2/16/2004', 2, 'c', 20, 20),
(30003, '4/18/2004', 3, 'b', 15, null),
(30004, '4/18/2004', 3, 'b', 20, null),	
(30007, '9/7/2004', 3, 'c', 30, null);
```