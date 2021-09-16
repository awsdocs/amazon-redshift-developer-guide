# Loading a column of the GEOMETRY data type<a name="copy-usage_notes-spatial-data"></a>

You can only COPY to `GEOMETRY` columns from data in a character\-delimited text file, such as a CSV file\.  The data must be in the hexadecimal form of the well\-known binary format \(either WKB or EWKB\) or the well\-known text format \(either WKT or EWKT\) and fit within the maximum size of a single input row to the COPY command\.  For more information, see [COPY](r_COPY.md)\. 

For more information about the `GEOMETRY` data type, see [Querying spatial data in Amazon Redshift](geospatial-overview.md)\.