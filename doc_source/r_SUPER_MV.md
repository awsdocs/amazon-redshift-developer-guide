# Using SUPER data type with materialized views<a name="r_SUPER_MV"></a>

Amazon Redshift extends the capability of materialized views to work with the SUPER data type and PartiQL in materialized views\. SQL and PartiQL queries can be precomputed using incremental materialized views\. For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

Once you have stored your schemaless and semistructured data into SUPER, you can use PartiQL materialized views to introspect the data and shred them into materialized views\. 