# Step 8: Evaluate the results<a name="tutorial-tuning-tables-evaluate"></a>

You tested load times, storage requirements, and query execution times before and after tuning the tables, and recorded the results\. 

The following table shows the example results for the cluster that was used for this tutorial\. Your results will be different, but should show similar improvements\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-evaluate.html)

**Load time** 

Load time increased by 17\.5%\. 

Sorting, compression, and distribution increase load time\. In particular, in this case, you used automatic compression, which increases the load time for empty tables that don't already have compression encodings\. Subsequent loads to the same tables would be faster\. You also increased load time by using ALL distribution\. You could reduce load time by using EVEN or DISTKEY distribution instead for some of the tables, but that decision needs to be weighed against query performance\. 

**Storage requirements** 

Storage requirements were reduced by 45\.4%\. 

Some of the storage improvement from using columnar compression was offset by using ALL distribution on some of the tables\. Again, you could improve storage use by using EVEN or DISTKEY distribution instead for some of the tables, but that decision needs to be weighed against query performance\. 

**Distribution** 

You verified that there is no distribution skew as a result of your distribution choices\. 

By checking the EXPLAIN plan, you saw that data redistribution was eliminated for the test queries\. 

**Query execution time** 

Total query execution time was reduced by 31\.4%\. 

The improvement in query performance was due to a combination of optimizing sort keys, distribution styles, and compression\. Often, query performance can be improved even further by rewriting queries and configuring workload management \(WLM\)\. For more information, see [Tuning query performance](c-optimizing-query-performance.md)\.

## Next step<a name="next-step-evaluate"></a>

[Step 9: Clean up your resources](tutorial-tuning-tables-clean-up.md)