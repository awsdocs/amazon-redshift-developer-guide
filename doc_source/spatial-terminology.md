# Terminology for Amazon Redshift spatial data<a name="spatial-terminology"></a>

The following terms are used to describe some Amazon Redshift spatial functions\. 

## Geometric validity<a name="spatial-terminology-validity"></a>

Geometric algorithms used by Amazon Redshift assume that the input geometry is a valid geometry\. If an input to an algorithm is not valid, then the result is undefined\. The following section describes the geometric validity definitions used by Amazon Redshift for each geometry subtype\.

Point  
A point is considered to be valid if all point coordinates are finite floating point numbers\.

Linestring  
 A linestring is considered to be valid if any of the following conditions are true:   
+ The linestring is empty; that is, it contains no points\. 
+ All points in a nonempty linestring have coordinates that are finite floating point numbers\. 
+ The linestring, if not empty, must be one\-dimensional; that is, it can't degenerate to a point\. 
Linestrings can have duplicate consecutive points\.  
Linestrings can have self\-intersections\.

Polygon  
A polygon is considered to be valid if any of the following conditions are true:   
+ The polygon is empty; that is, it contains no rings\.
+ If not empty, a polygon is valid if all of the following conditions are true: 
  + All rings of the polygon are valid\. A ring is considered to be valid if all the following conditions are true:
    + All points of the ring have coordinates that are finite floating point numbers\.
    + The ring is closed; that is, its first point and its last point coincide\.
    + The ring doesn't have any self\-intersections\.
    + The ring is two\-dimensional\.
  + The rings of the polygon have consistent orientations\. That is, if you traverse any ring, the interior of the polygon is either to your right or to your left\. This means that if a polygon's exterior ring is oriented clockwise or counterclockwise, all the polygon's interior rings must have the same counterclockwise or clockwise orientation\. 
  + All interior rings must be within the exterior ring of the polygon\.
  + Interior rings can't be nested; that is, an interior ring can't be within another interior ring\.
  + Interior and exterior rings can only intersect at a finite number of points\.
  + The interior of the polygon must be simply connected\.

Multipoint  
If not empty, a multipoint is considered to be valid if all points are valid according to the point validity definition\. A multipoint can have duplicate points\.

Multiinestring  
A multilinestring is considered to be valid if any of the following conditions are true:   
+ The multilinestring is empty; that is, it contains no linestrings\. 
+ All linestrings in a nonempty multilinestring are valid according to the linestring validity definition\. 
A nonempty multilinestring that consists of only empty linestrings is considered to be valid\.   
Empty linestrings in a multilinestring don't affect its validity\.   
A multilinestring can have linestrings with duplicate consecutive points\.  
A multilinestring can have self\-intersections\.

Multipolygon  
A multipolygon is considered to be valid if any of the following conditions are true:   
+ The multipolygon doesn't contain any polygons \(it is empty\)\. 
+ The multipolygon is not empty and all of the following are true:
  + All polygons in the multipolygon are valid\.
  + No two polygons in the multipolygon can intersect at an infinite number of points\. In particular, this implies that the interior of any two polygons can't intersect and that they can only touch at a finite number of points\.
Empty polygons in multipolygons don't invalidate a multipolygon\.

Geometry collection  
A geometry collection is considered to be valid if any of the following conditions are true:   
+ The geometry collection is empty; that is, it doesn't contain any geometries\. 
+ All geometries in a nonempty geometry collection are valid\. 
This definition still applies, although in a recursive manner, for nested geometry collections\. 

## Geometric simplicity<a name="spatial-terminology-simplicity"></a>

Geometric algorithms used by Amazon Redshift assume that the input geometry is a valid geometry\. If an input to an algorithm is not valid, then the simplicity check is undefined\. The following section describes the geometric simplicity definitions used by Amazon Redshift for each geometry subtype\.   

Point  
A valid point is always considered to be simple\. 

Linestring  
A valid linestring is considered to be simple if any of the following conditions are true:   
+ The linestring is empty\. 
+ The linestring is not empty and all of the following conditions are true: 
  + It has no duplicate consecutive points\.
  + It has no self\-intersections, except possibly for its first point and last point, which can coincide\. In other words, the linestring can't have self\-intersections except at boundary points\.

Polygon  
A valid polygon is considered to be simple if it doesn't contain any duplicate consecutive points\. 

Multipoint  
A valid multipoint is considered to be simple if any of the following conditions are true:   
+ The multipoint is empty; that is, it contains no points\. 
+ No two nonempty points of the multipoint coincide\. 

Multiinestring  
A valid multilinestring is considered to be simple if any of the following conditions are true:   
+ The multilinestring is empty\. 
+ The multilinestring is nonempty and all of the following conditions are true: 
  + All its linestrings are simple\.
  + Any two linestrings of the multilinestring don't intersect, except at points that are boundary points of the two linestrings\.
A nonempty multilinestring that consists of empty linestrings only is considered to be empty\.   
Empty linestrings in a multilinestring don't affect its simplicity\.   
A closed linestring in a multilinestring can't intersect with any other linestring in the multilinestring\.  
A multilinestring can't have linestrings with duplicate consecutive points\.

Multipolygon  
A valid multipolygon is considered to be simple if it doesn't contain any duplicate consecutive points\. 

Geometry collection  
A valid geometry collection is considered to be simple if any of the following conditions are true:   
+ The geometry collection is empty; that is, it doesn't contain any geometries\. 
+ All geometries in a nonempty geometry collection are simple\. 
This definition still applies, although in a recursive manner, for nested geometry collections\. 