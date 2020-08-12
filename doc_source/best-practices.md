# Amazon Redshift best practices<a name="best-practices"></a>

Following, you can find best practices for planning a proof of concept, designing tables, loading data into tables, and writing queries for Amazon Redshift, and also a discussion of working with Amazon Redshift Advisor\. 

Amazon Redshift is not the same as other SQL database systems\. To fully realize the benefits of the Amazon Redshift architecture, you must specifically design, build, and load your tables to use massively parallel processing, columnar data storage, and columnar data compression\. If your data loading and query execution times are longer than you expect, or longer than you want, you might be overlooking key information\. 

If you are an experienced SQL database developer, we strongly recommend that you review this topic before you begin developing your Amazon Redshift data warehouse\. 

If you are new to developing SQL databases, this topic is not the best place to start\. We recommend that you begin by reading [Getting started using databases](c_intro_to_admin.md) and trying the examples yourself\. 

In this topic, you can find an overview of the most important development principles, along with specific tips, examples, and best practices for implementing those principles\. No single practice can apply to every application\. You should evaluate all of your options before finalizing a database design\. For more information, see [Designing tables](t_Creating_tables.md), [Loading data](t_Loading_data.md), [Tuning query performance](c-optimizing-query-performance.md), and the reference chapters\. 

**Topics**
+ [Conducting a proof of concept for Amazon Redshift](proof-of-concept-playbook.md)
+ [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md)
+ [Amazon Redshift best practices for loading data](c_loading-data-best-practices.md)
+ [Amazon Redshift best practices for designing queries](c_designing-queries-best-practices.md)
+ [Working with recommendations from Amazon Redshift Advisor](advisor.md)