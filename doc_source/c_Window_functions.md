# Window Functions<a name="c_Window_functions"></a>

**Topics**
+ [Window Function Syntax Summary](r_Window_function_synopsis.md)
+ [AVG Window Function](r_WF_AVG.md)
+ [COUNT Window Function](r_WF_COUNT.md)
+ [CUME\_DIST Window Function](r_WF_CUME_DIST.md)
+ [DENSE\_RANK Window Function](r_WF_DENSE_RANK.md)
+ [FIRST\_VALUE and LAST\_VALUE Window Functions](r_WF_first_value.md)
+ [LAG Window Function](r_WF_LAG.md)
+ [LEAD Window Function](r_WF_LEAD.md)
+ [LISTAGG Window Function](r_WF_LISTAGG.md)
+ [MAX Window Function](r_WF_MAX.md)
+ [MEDIAN Window Function](r_WF_MEDIAN.md)
+ [MIN Window Function](r_WF_MIN.md)
+ [NTH\_VALUE Window Function](r_WF_NTH.md)
+ [NTILE Window Function](r_WF_NTILE.md)
+ [PERCENT\_RANK Window Function](r_WF_PERCENT_RANK.md)
+ [PERCENTILE\_CONT Window Function](r_WF_PERCENTILE_CONT.md)
+ [PERCENTILE\_DISC Window Function](r_WF_PERCENTILE_DISC.md)
+ [RANK Window Function](r_WF_RANK.md)
+ [RATIO\_TO\_REPORT Window Function](r_WF_RATIO_TO_REPORT.md)
+ [ROW\_NUMBER Window Function](r_WF_ROW_NUMBER.md)
+ [STDDEV\_SAMP and STDDEV\_POP Window Functions](r_WF_STDDEV.md)
+ [SUM Window Function](r_WF_SUM.md)
+ [VAR\_SAMP and VAR\_POP Window Functions](r_WF_VARIANCE.md)
+ [Window Function Examples](r_Window_function_examples.md)

 Window functions provide application developers the ability to create analytic business queries more efficiently\. Window functions operate on a partition or "window" of a result set, and return a value for every row in that window\. In contrast, nonwindowed functions perform their calculations with respect to every row in the result set\. Unlike group functions that aggregate result rows, all rows in the table expression are retained\. 

 The values returned are calculated by utilizing values from the sets of rows in that window\. The window defines, for each row in the table, a set of rows that is used to compute additional attributes\. A window is defined using a window specification \(the OVER clause\), and is based on three main concepts: 
+  *Window partitioning,* which forms groups of rows \(PARTITION clause\) 
+  *Window ordering*, which defines an order or sequence of rows within each partition \(ORDER BY clause\) 
+  *Window frames*, which are defined relative to each row to further restrict the set of rows \(ROWS specification\) 

Window functions are the last set of operations performed in a query except for the final ORDER BY clause\. All joins and all WHERE, GROUP BY, and HAVING clauses are completed before the window functions are processed\. Therefore, window functions can appear only in the select list or ORDER BY clause\. Multiple window functions can be used within a single query with different frame clauses\. Window functions may be present in other scalar expressions, such as CASE\. 

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