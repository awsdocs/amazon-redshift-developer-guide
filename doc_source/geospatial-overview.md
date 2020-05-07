# Querying spatial data in Amazon Redshift<a name="geospatial-overview"></a>

Spatial data describes the position and shape of a geometry in a defined space \(a spatial reference system\)\. Amazon Redshift supports spatial data with the `GEOMETRY` data type, which contains spatial data and optionally its spatial reference system identifier \(SRID\)\. 

Spatial data contains geometric data that can be used to represent geographic features\. Examples of this type of data include weather reports, map directions, tweets with geographic positions, store locations, and airline routes\. Spatial data plays an important role in business analytics, reporting, and forecasting\. 

You can query spatial data with Amazon Redshift SQL functions\. Spatial data contains geometric values for an object\. 

Using spatial data, you can run queries to do the following:
+ Find the distance between two points\.
+ Check whether one area \(polygon\) contains another\.
+ Check whether one linestring intersects another linestring or polygon\.

You can use the `GEOMETRY` data type to hold the values of spatial data\. A `GEOMETRY` value in Amazon Redshift can define two\-dimensional \(2D\) geometry primitives\. Currently, Amazon Redshift doesn't support 3D or 4D geometry primitives\. For more information about geometry primitives, see [Well\-known text representation of geometry](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) in Wikipedia\. 

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

For details about SQL functions to query spatial data, see [Spatial functions](geospatial-functions.md)\. 

**Topics**
+ [Limitations when using spatial data with Amazon Redshift](spatial-limitations.md)