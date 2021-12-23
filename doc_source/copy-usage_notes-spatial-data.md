# Loading a column of the GEOMETRY or GEOGRAPHY data type<a name="copy-usage_notes-spatial-data"></a>

You can COPY to `GEOMETRY` or `GEOGRAPHY` columns from data in a character\-delimited text file, such as a CSV file\.  The data must be in the hexadecimal form of the well\-known binary format \(either WKB or EWKB\) or the well\-known text format \(either WKT or EWKT\) and fit within the maximum size of a single input row to the COPY command\.  For more information, see [COPY](r_COPY.md)\. 

For information about how to load from a shapefile, see [Loading a shapefile into Amazon Redshift](spatial-copy-shapefile.md)\.

For more information about the `GEOMETRY` or `GEOGRAPHY` data type, see [Querying spatial data in Amazon Redshift](geospatial-overview.md)\.