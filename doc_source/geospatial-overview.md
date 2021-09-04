# Querying spatial data in Amazon Redshift<a name="geospatial-overview"></a>

*Spatial data* describes the position and shape of a geometry in a defined space \(a spatial reference system\)\. Amazon Redshift supports spatial data with the `GEOMETRY` data type, which contains spatial data and optionally the data's spatial reference system identifier \(SRID\)\. 

Spatial data contains geometric data that you can use to represent geographic features\. Examples of this type of data include weather reports, map directions, tweets with geographic positions, store locations, and airline routes\. Spatial data plays an important role in business analytics, reporting, and forecasting\. 

You can query spatial data with Amazon Redshift SQL functions\. Spatial data contains geometric values for an object\. 

Using spatial data, you can run queries to do the following:
+ Find the distance between two points\.
+ Check whether one area \(polygon\) contains another\.
+ Check whether one linestring intersects another linestring or polygon\.

You can use the `GEOMETRY` data type to hold the values of spatial data\. A `GEOMETRY` value in Amazon Redshift can define two\-dimensional \(2D\), three\-dimensional \(3DZ\), two\-dimensional with a measure \(3DM\), and four\-dimensional \(4D\) geometry primitive data types:
+ A two\-dimensional \(2D\) geometry is specified by two Cartesian coordinates \(x, y\) in a plane\.
+ A three\-dimensional \(3DZ\) geometry is specified by three Cartesian coordinates \(x, y, z\) in space\.
+ A two\-dimensional with measure \(3DM\) geometry is specified by three coordinates \(x, y, m\), where the first two are Cartesian coordinates in a plane and the third is a measurement\.
+ A four\-dimensional \(4D\) geometry is specified by four coordinates \(x, y, z, m\), where the first three are Cartesian coordinates in a space and the fourth is a measurement\.

For more information about geometry primitive data types, see [Well\-known text representation of geometry](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) in Wikipedia\.   

The `GEOMETRY` data type has the following subtypes: 
+ `POINT`
+ `LINESTRING`
+ `POLYGON`
+ `MULTIPOINT`
+ `MULTILINESTRING`
+ `MULTIPOLYGON`
+ `GEOMETRYCOLLECTION`

There are Amazon Redshift SQL functions that support the following representations of geometric data:
+ GeoJSON
+ Well\-known text \(WKT\) 
+ Extended well\-known text \(EWKT\)
+ Well\-known binary \(WKB\) representation 
+ Extended well\-known binary \(EWKB\)

Amazon Redshift provides many SQL functions to query spatial data\. Except for the `ST_IsValid` function, spatial functions that accept a `GEOMETRY` object as an argument expect this `GEOMETRY` object to be a valid geometry\. If the `GEOMETRY` object isn't valid, then the behavior of the spatial function is undefined\. For more information about validity, see [Geometric validity](spatial-terminology.md#spatial-terminology-validity)\. 

For details about SQL functions to query spatial data, see [Spatial functions](geospatial-functions.md)\. 

For details about loading spatial data, see [Loading a column of the GEOMETRY data type](copy-usage_notes-spatial-data.md)\. 

**Topics**
+ [Tutorial: Using spatial SQL functions with Amazon Redshift](spatial-tutorial.md)
+ [Loading a shapefile into Amazon Redshift](spatial-copy-shapefile.md)
+ [Terminology for Amazon Redshift spatial data](spatial-terminology.md)
+ [Limitations when using spatial data with Amazon Redshift](spatial-limitations.md)