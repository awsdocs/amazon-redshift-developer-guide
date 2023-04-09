# SUPER type<a name="r_SUPER_type"></a>

Use the SUPER data type to store semistructured data or documents as values\. 

Semistructured data doesn't conform to the rigid and tabular structure of the relational data model used in SQL databases\. It contains tags that reference distinct entities within the data\. They can contain complex values such as arrays, nested structures, and other complex structures that are associated with serialization formats, such as JSON\. The SUPER data type is a set of schemaless array and structure values that encompass all other scalar types of Amazon Redshift\.

The SUPER data type supports up to 16 MB of data for an individual SUPER field or object\. For more information on the SUPER data type, including examples of implementing it in a table, see [Ingesting and querying semistructured data in Amazon Redshift](super-overview.md)\.

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

For information about how Amazon Redshift uses PartiQL to enable navigation into arrays and structures, see [Navigation](query-super.md#navigation)\. For information about how Amazon Redshift uses the PartiQL syntax to iterate over SUPER arrays by navigating the array using the FROM clause of a query, see [Unnesting queries](query-super.md#unnest)\.

For information about how Amazon Redshift uses dynamic typing to process schemaless SUPER data without the need to declare the data types before you use them in your query, see [Dynamic typing](query-super.md#dynamic-typing-lax-processing)\.