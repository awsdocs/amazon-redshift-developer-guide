# Window Function Examples<a name="r_Window_function_examples"></a>


+ [AVG Window Function Examples](r_Examples_of_avg_WF.md)
+ [COUNT Window Function Examples](r_Examples_of_count_WF.md)
+ [CUME\_DIST Window Function Examples](r_Examples_of_CUME_DIST_WF.md)
+ [DENSE\_RANK Window Function Examples](r_Examples_of_dense_rank_WF.md)
+ [FIRST\_VALUE and LAST\_VALUE Window Function Examples](r_Examples_of_firstlast_WF.md)
+ [LAG Window Function Examples](r_Examples_of_LAG_WF.md)
+ [LEAD Window Function Examples](r_Examples_of_LEAD_WF.md)
+ [LISTAGG Window Function Examples](r_Examples_of_LISTAGG_WF.md)
+ [MAX Window Function Examples](r_Examples_of_max_WF.md)
+ [MEDIAN Window Function Examples](r_Examples_of_median_WF.md)
+ [MIN Window Function Examples](r_Examples_of_min_WF.md)
+ [NTH\_VALUE Window Function Examples](r_Examples_of_NTH_WF.md)
+ [NTILE Window Function Examples](r_Examples_of_NTILE_WF.md)
+ [PERCENT\_RANK Window Function Examples](r_Examples_of_PERCENT_RANK_WF.md)
+ [PERCENTILE\_CONT Window Function Examples](r_Examples_of_PERCENTILE_CONT_WF.md)
+ [PERCENTILE\_DISC Window Function Examples](r_Examples_of_PERCENTILE_DISC_WF.md)
+ [RANK Window Function Examples](r_Examples_of_rank_WF.md)
+ [RATIO\_TO\_REPORT Window Function Examples](r_Examples_of_RATIO_TO_REPORT_WF.md)
+ [ROW\_NUMBER Window Function Example](r_Examples_of_WF_ROW_NUMBER_WF.md)
+ [STDDEV\_POP and VAR\_POP Window Function Examples](r_Examples_stddev_variance_WF.md)
+ [SUM Window Function Examples](r_Examples_of_sum_WF.md)
+ [Unique Ordering of Data for Window Functions](r_Examples_order_by_WF.md)

 This section provides examples for using the window functions\. 

Some of the window function examples in this section use a table named WINSALES, which contains 11 rows: 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Window_function_examples.html)

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