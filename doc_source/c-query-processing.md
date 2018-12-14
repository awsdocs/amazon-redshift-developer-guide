# Query Processing<a name="c-query-processing"></a>

Amazon Redshift routes a submitted SQL query through the parser and optimizer to develop a query plan\. The execution engine then translates the query plan into code and sends that code to the compute nodes for execution\.

**Topics**
+ [Query Planning And Execution Workflow](c-query-planning.md)
+ [Reviewing Query Plan Steps](reviewing-query-plan-steps.md)
+ [Query Plan](c-the-query-plan.md)
+ [Factors Affecting Query Performance](c-query-performance.md)