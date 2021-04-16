# Loading a shapefile into Amazon Redshift<a name="spatial-copy-shapefile"></a>

You can use the COPY command to ingest Esri shapefiles stored in Amazon S3 into Amazon Redshift tables\. A *shapefile* stores the geometric location and attribute information of geographic features in a vector format\. The shapefile format can spatially describe spatial objects such as points, lines, and polygons\. For more information about a shapefile, see [Shapefile](https://en.wikipedia.org/wiki/Shapefile) in Wikipedia\.   

The COPY command supports the data format parameter `SHAPEFILE`\. By default, the first column of the shapefile is either a `GEOMETRY` or `IDENTITY` column\. All subsequent columns follow the order specified in the shapefile\. However, the target table doesn't need to be in this exact layout because you can use COPY column mapping to define the order\. For information about the COPY command shapefile support, see [SHAPEFILE](copy-parameters-data-format.md#copy-shapefile)\.

In some cases, the resulting geometry size might be greater than the maximum for storing a geometry in Amazon Redshift\. If so, you can use the COPY option `SIMPLIFY` or `SIMPLIFY AUTO` to simplify the geometries during ingestion as follows:
+ Specify `SIMPLIFY tolerance` to simplify all geometries during ingestion using the Ramer\-Douglas\-Peucker algorithm and the given tolerance\. 
+ Specify `SIMPLIFY AUTO` without tolerance to simplify only geometries that are larger than the maximum size using the Ramer\-Douglas\-Peucker algorithm\. This approach calculates the minimum tolerance that is large enough to store the object within the maximum size limit\. 
+ Specify `SIMPLIFY AUTO max_tolerance` to simplify only geometries that are larger than the maximum size using the Ramer\-Douglas\-Peucker algorithm and the automatically calculated tolerance\. This approach makes sure that the tolerance doesn't exceed the maximum tolerance\. 

For information about the maximum size of a `GEOMETRY` data value, see [Limitations when using spatial data with Amazon Redshift](spatial-limitations.md)\.

In some cases, the tolerance is low enough that the record can't shrink below the maximum size of a `GEOMETRY` data value\. In these cases, you can use the `MAXERROR` option of the COPY command to ignore all or up to a certain number of ingestion errors\.

The COPY command also supports loading GZIP shapefiles\. To do this, specify the COPY GZIP parameter\. With this option, all shapefile components must be independently compressed and share the same compression suffix\. 

Query the `SVL_SPATIAL_SIMPLIFY` system view to view which records have been simplified, along with the calculated tolerance\. When you specify `SIMPLIFY tolerance`, this view contains a record for each COPY operation\. Otherwise, it contains a record for each simplified geometry\. For more information, see [SVL\_SPATIAL\_SIMPLIFY](r_SVL_SPATIAL_SIMPLIFY.md)\. 

For examples of loading a shapefile, see [Loading a shapefile into Amazon Redshift](r_COPY_command_examples.md#copy-example-spatial-copy-shapefile)\.