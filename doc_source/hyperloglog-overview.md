# Using HyperLogLog sketches in Amazon Redshift<a name="hyperloglog-overview"></a>

*HyperLogLog* is an algorithm used for estimating the cardinality of a multiset\. *Cardinality* refers to the number of distinct values in a multiset\. For example, in the set of \{4,3,6,2,2,6,4,3,6,2,2,3\}, the cardinality is 4 with distinct values of 4, 3, 6, and 2\. 

The precision of the HyperLogLog algorithm \(also known as m value\) can affect the accuracy of the estimated cardinality\. During the cardinality estimation, Amazon Redshift uses a default precision value of 15\. This value can be up to 26 for smaller datasets\. Thus, the average relative error ranges between 0\.01–0\.6%\.

When calculating the cardinality of a multiset, the HyperLogLog algorithm generates a construct called an HLL sketch\. An *HLL sketch *encapsulates information about the distinct values in a multiset\. The Amazon Redshift data type HLLSKETCH represents such sketch values\. This data type can be used to store sketches in an Amazon Redshift table\. Additionally, Amazon Redshift supports operations that can be applied to HLLSKETCH values as aggregate and scalar functions\. You can use these functions to extract the cardinality of an HLLSKETCH and combine multiple HLLSKETCH values\. 

The HLLSKETCH data type offers significant query performance benefits when extracting the cardinality from large datasets\. You can preaggregate these datasets using HLLSKETCH values and store them in tables\. Amazon Redshift can extract the cardinality directly from the stored HLLSKETCH values without accessing the underlying datasets\.

When processing HLL sketches, Amazon Redshift performs optimizations that minimize the memory footprint of the sketch and maximize the precision of the extracted cardinality\. Amazon Redshift uses two representations for HLL sketches, sparse and dense\. An HLLSKETCH starts in sparse format\. As new values are inserted into it, its size increases\. After its size reaches the size of the dense representation, Amazon Redshift automatically converts the sketch from sparse to dense\.

Amazon Redshift imports, exports, and prints an HLLSKETCH as JSON when the sketch is in a sparse format\. Amazon Redshift imports, exports, and prints an HLLSKETCH as a Base64 string when the sketch is in a dense format\. For more information about UNLOAD, see [Unloading the HLLSKETCH data type](r_UNLOAD.md#unload-usage-hll)\. To import text or comma\-separated value \(CSV\) data into Amazon Redshift, use the COPY command\. For more information, see [Loading the HLLSKETCH data type](copy-usage_notes-hll.md)\.

For information about functions used with HyperLogLog, see [HyperLogLog functions](hyperloglog-functions.md)\.

**Topics**
+ [Considerations](hyperloglog-functions-usage-notes.md)
+ [Limitations](hyperloglog-functions-limitations.md)
+ [Examples](r_HLL-examples.md)