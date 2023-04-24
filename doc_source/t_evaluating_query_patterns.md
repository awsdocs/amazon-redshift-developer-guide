# Evaluating query patterns<a name="t_evaluating_query_patterns"></a>

 Choosing distribution styles is only one aspect of database design\. Consider distribution styles within the context of the entire system, balancing distribution with other important factors such as cluster size, compression encoding methods, sort keys, and table constraints\. 

 Test your system with data that is as close to real data as possible\. 

To make good choices for distribution styles, you must understand the query patterns for your Amazon Redshift application\. Identify the most costly queries in your system and base your initial database design on the demands of those queries\. Factors that determine the total cost of a query include how long the query takes to run and how much computing resources it consumes\. Other factors that determine query cost are how often it is run, and how disruptive it is to other queries and database operations\. 

 Identify the tables that are used by the most costly queries, and evaluate their role in query runtime\. Consider how the tables are joined and aggregated\. 

 Use the guidelines in this section to choose a distribution style for each table\. When you have done so, create the tables and load them with data that is as close as possible to real data\. Then test the tables for the types of queries that you expect to use\. You can evaluate the query explain plans to identify tuning opportunities\. Compare load times, storage space, and query runtimes to balance your system's overall requirements\. 