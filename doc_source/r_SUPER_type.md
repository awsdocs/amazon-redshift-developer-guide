# SUPER type<a name="r_SUPER_type"></a>


|  | 
| --- |
| This is prerelease documentation for the semistructured data feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

Use the SUPER data type to store semistructured data or documents as values\. 

Semistructured data doesn't conform to the rigid and tabular structure of the relational data model used in SQL databases\. It contains tags that reference distinct entities within the data\. They can contain complex values such as arrays, nested structures, and other complex structures that are associated with serialization formats, such as JSON\. The SUPER data type is a set of schemaless array and structure values that encompass all other scalar types of Amazon Redshift\.

The SUPER data type has the following properties:
+ An Amazon Redshift scalar value:
  + A null
  + A boolean
  + A number, such as smallint, integer, bigint, decimal, or floating point \(such as float4 or float8\)
  + A string value, such as varchar or char
+ A complex value:
  + An array of values, including scalar or complex
  + A structure, also known as tuple or object, that is a map of attribute names and values \(scalar or complex\)

Any of the two types of complex values contain their own scalars or complex values without having any restrictions for regularity\.

The SUPER data type supports the persistence of semistructured data in a schemaless form\. Although hierarchical data model can change, the old versions of data can coexist in the same SUPER column\. 

For information about how Amazon Redshift uses PartiQL to enable navigation into arrays and structures, see [Navigation](query-super.md#navigation)\. For information about how Amazon Redshift uses the PartiQL syntax to iterate over SUPER arrays by navigating the array using the FROM clause of a query, see [Unnesting](query-super.md#unnest)\.

For information about how Amazon Redshift uses dynamic typing to process schemaless SUPER data without the need to declare the data types before you use them in your query, see [Dynamic typing](query-super.md#dynamic-typing-lax-processing)\.