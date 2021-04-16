# Query processing<a name="c-query-processing"></a>

Amazon Redshift routes a submitted SQL query through the parser and optimizer to develop a query plan\. The execution engine then translates the query plan into code and sends that code to the compute nodes for execution\.

**Topics**
+ [Query planning and execution workflow](c-query-planning.md)
+ [Query plan](c-the-query-plan.md)
+ [Reviewing query plan steps](reviewing-query-plan-steps.md)
+ [Factors affecting query performance](c-query-performance.md)