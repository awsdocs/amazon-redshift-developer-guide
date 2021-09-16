# Tutorial: Using spatial SQL functions with Amazon Redshift<a name="spatial-tutorial"></a>

This tutorial demonstrates how to use some of the spatial SQL functions with Amazon Redshift\. 

To do this, you query two tables using spatial SQL functions\. The tutorial uses data from public datasets that correlate location data of rental accommodations with postal codes in Berlin, Germany\. 

**Topics**
+ [Prerequisites](#spatial-tutorial-prerequisites)
+ [Step 1: Create tables and load test data](#spatial-tutorial-test-data)
+ [Step 2: Query spatial data](#spatial-tutorial-query)
+ [Step 3: Clean up your resources](#spatial-tutorial-clean-up)

## Prerequisites<a name="spatial-tutorial-prerequisites"></a>

For this tutorial, you need the following resources:
+ An existing Amazon Redshift cluster and database that you can access and update\. In the existing cluster, you create tables, load sample data, and run SQL queries to demonstrate spatial functions\. Your cluster should have at least two nodes\. To learn how to create a cluster, follow the steps in [Amazon Redshift Getting Started Guide](https://docs.aws.amazon.com/redshift/latest/gsg/)\. 
+ To use the Amazon Redshift query editor, make sure that your cluster is in an AWS Region that supports the query editor\. For more information, see [Querying a database using the query editor](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor.html) in the *Amazon Redshift Cluster Management Guide*\. 
+ AWS credentials for your Amazon Redshift cluster that allow it to load test data from Amazon S3\. For information about how to create new access keys, see [Administering Access Keys for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html) in the *IAM User Guide*\.
+ The AWS Identity and Access Management \(IAM\) role named `mySpatialDemoRole`, which has the managed policy `AmazonS3ReadOnlyAccess` attached to read Amazon S3 data\. To create a role with permission to load data from an Amazon S3 bucket, see [Authorizing COPY, UNLOAD, and CREATE EXTERNAL SCHEMA operations using IAM roles](https://docs.aws.amazon.com/redshift/latest/mgmt/copy-unload-iam-role.html) in the *Amazon Redshift Cluster Management Guide*\. 
+ After you create the IAM role `mySpatialDemoRole`, that role needs an association with your Amazon Redshift cluster\. For more information on how to create that association, see [Authorizing COPY, UNLOAD, and CREATE EXTERNAL SCHEMA operations using IAM roles](https://docs.aws.amazon.com/redshift/latest/mgmt/copy-unload-iam-role.html) in the *Amazon Redshift Cluster Management Guide*\. 

## Step 1: Create tables and load test data<a name="spatial-tutorial-test-data"></a>

The source data used by this tutorial is in files named `accommodations.csv` and `zipcodes.csv`\. 

The `accommodations.csv` file is open\-source data from insideairbnb\.com\. The `zipcodes.csv` file provides postal codes that are open\-source data from the national statistics institute of Berlin\-Brandenburg in Germany \(Amt für Statistik Berlin\-Brandenburg\)\. Both data sources are provided under a Creative Commons license\. The data is limited to the Berlin, Germany, region\. These files are located in an Amazon S3 public bucket to use with this tutorial\. 

You can optionally download the source data from the following Amazon S3 links:
+ [ Source data for the `accommodations` table](https://s3.amazonaws.com/redshift-downloads/spatial-data/accommodations.csv)\. 
+ [ Source data for the `zipcode` table](https://s3.amazonaws.com/redshift-downloads/spatial-data/zipcode.csv)\. 

Use the following procedure to create tables and load test data\. 

**To create tables and load test data**

1. Open the Amazon Redshift query editor\. For more information on working with the query editor, see [Querying a database using the query editor](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor.html) in the *Amazon Redshift Cluster Management Guide*\. 

1. Drop any tables used by this tutorial if they already exist in your database\. For more information, see [Step 3: Clean up your resources](#spatial-tutorial-clean-up)\. 

1. Create the `accommodations` table to store each accommodation's geographical location \(longitude and latitude\), the name of the listing, and other business data\. 

   This tutorial explores room rentals in Berlin, Germany\. The `shape` column stores geographic points of the location of accommodations\. The other columns contain information about the rental\. 

   To create the `accommodations` table, run the following SQL statement in the Amazon Redshift query editor\. 

   ```
   CREATE TABLE public.accommodations (
     id INTEGER PRIMARY KEY,
     shape GEOMETRY,
     name VARCHAR(100),
     host_name VARCHAR(100),
     neighbourhood_group VARCHAR(100),
     neighbourhood VARCHAR(100),
     room_type VARCHAR(100),
     price SMALLINT,
     minimum_nights SMALLINT,
     number_of_reviews SMALLINT,
     last_review DATE,
     reviews_per_month NUMERIC(8,2),
     calculated_host_listings_count SMALLINT, 
     availability_365 SMALLINT
   );
   ```

1. Create the `zipcode` table in the query editor to store Berlin postal codes\. 

   A *postal code* is defined as a polygon in the `wkb_geometry` column\. The rest of the columns describe additional spatial metadata about the postal code\. 

   To create the `zipcode` table, run the following SQL statement in the Amazon Redshift query editor\. 

   ```
   CREATE TABLE public.zipcode (
     ogc_field INTEGER PRIMARY KEY NOT NULL,
     wkb_geometry GEOMETRY,
     gml_id VARCHAR(256),
     spatial_name VARCHAR(256),
     spatial_alias VARCHAR(256),
     spatial_type VARCHAR(256)
    );
   ```

1. Load the tables using sample data\. 

   The sample data for this tutorial is provided in an Amazon S3 bucket that allows read access to all authenticated AWS users\. Make sure that you provide valid AWS credentials that permit access to Amazon S3\. 

   To load test data to your tables, run the following COPY commands\. Replace *`account-number`* with your own AWS account number\. The segment of the credentials string that is enclosed in single quotation marks can't contain any spaces or line breaks\. 

   ```
   COPY public.accommodations 
   FROM 's3://redshift-downloads/spatial-data/accommodations.csv' 
   DELIMITER ';' 
   IGNOREHEADER 1 REGION 'us-east-1'
   CREDENTIALS 'aws_iam_role=arn:aws:iam::account-number:role/mySpatialDemoRole';
   ```

   ```
   COPY public.zipcode 
   FROM 's3://redshift-downloads/spatial-data/zipcode.csv' 
   DELIMITER ';' 
   IGNOREHEADER 1 REGION 'us-east-1'
   CREDENTIALS 'aws_iam_role=arn:aws:iam::account-number:role/mySpatialDemoRole';
   ```

1. Verify that each table loaded correctly by running the following commands\. 

   ```
   select count(*) from accommodations;
   ```

   ```
   select count(*) from zipcode;
   ```

   The following results show the number of rows in each table of test data\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/spatial-tutorial.html)

## Step 2: Query spatial data<a name="spatial-tutorial-query"></a>

After your tables are created and loaded, you can query them using SQL SELECT statements\. The following queries demonstrate some of the information that you can retrieve\. You can write many other queries that use spatial functions to satisfy your needs\. 

**To query spatial data**

1. Query to get the count of the total number of listings stored in the `accommodations` table, as shown following\. The spatial reference system is World Geodetic System \(WGS\) 84, which has the unique spatial reference identifier 4326\.

   ```
   SELECT count(*) FROM public.accommodations WHERE ST_SRID(shape) = 4326;
   ```

   ```
    count
   -------
    22248
   ```

1. Fetch the geometry objects in well\-known text \(WKT\) format with some additional attributes\. Additionally, you can validate if this postal code data is also stored in World Geodetic System \(WGS\) 84, which uses the spatial reference ID \(SRID\) 4326\. Spatial data must be stored in the same spatial reference system to be interoperable\. 

   ```
   SELECT ogc_field, spatial_name, spatial_type, ST_SRID(wkb_geometry), ST_AsText(wkb_geometry) 
   FROM public.zipcode 
   ORDER BY spatial_name;
   ```

   ```
   ogc_field  spatial_name  spatial_type  st_srid  st_astext
   ---------------------------------------------------------------
   0           10115        Polygon        4326     POLYGON((...))
   4           10117        Polygon        4326     POLYGON((...))
   8           10119        Polygon        4326     POLYGON((...))
   ...
   (190 rows returned)
   ```

1. Select the polygon of Berlin Mitte \(10117\), a borough of Berlin, in GeoJSON format, its dimension, and the number of points in this polygon\. 

   ```
   SELECT ogc_field, spatial_name, ST_AsGeoJSON(wkb_geometry), ST_Dimension(wkb_geometry), ST_NPoints(wkb_geometry)
   FROM public.zipcode 
   WHERE spatial_name='10117';
   ```

   ```
   ogc_field  spatial_name  spatial_type                                   st_dimension  st_npoint
   -----------------------------------------------------------------------------------------------
   4           10117         {"type":"Polygon", "coordinates":[[[...]]]}    2             331
   ```

1. Run the following SQL command to view how many accommodations are within 500 meters of the Brandenburg Gate\. 

   ```
   SELECT count(*) 
   FROM public.accommodations 
   WHERE ST_DistanceSphere(shape, ST_GeomFromText('POINT(13.377704 52.516431)', 4326)) < 500;
   ```

   ```
   count
   ------
     29
   ```

1. Get the rough location of the Brandenburg Gate from data stored in the accommodations that are listed as nearby by running the following query\.

   This query requires a subselect\. It leads to a different count because the requested location is not the same as the previous query because it is closer to the accommodations\. 

   ```
   WITH poi(loc) as (
     SELECT st_astext(shape) FROM accommodations WHERE name LIKE '%brandenburg gate%'
   )
   SELECT count(*) 
   FROM accommodations a, poi p
   WHERE ST_DistanceSphere(a.shape, ST_GeomFromText(p.loc, 4326)) < 500;
   ```

   ```
   count
   ------
     60
   ```

1. Run the following query to show the details of all accommodations around the Brandenburg Gate, ordered by price in descending order\. 

   ```
   SELECT name, price, ST_AsText(shape) 
   FROM public.accommodations
   WHERE ST_DistanceSphere(shape, ST_GeomFromText('POINT(13.377704 52.516431)', 4326)) < 500
   ORDER BY price DESC;
   ```

   ```
   name                                                   price  st_astext
   -------------------------------------------------------------------------------------------------------
   DUPLEX APARTMENT/PENTHOUSE in 5* LOCATION! 7583        300    POINT(13.3826510209548 52.5159819722552)
   DUPLEX-PENTHOUSE IN FIRST LOCATION! 7582               300    POINT(13.3799997083855 52.5135918444834)
   ... 
   (29 rows returned)
   ```

1. Run the following query to retrieve the most expensive accommodation with its postal code\. 

   ```
   SELECT 
     a.price, a.name, ST_AsText(a.shape), 
     z.spatial_name, ST_AsText(z.wkb_geometry) 
   FROM accommodations a, zipcode z 
   WHERE price = 9000 AND ST_Within(a.shape, z.wkb_geometry);
   ```

   ```
   price   name                                 st_astext                                  spatial_name      st_astext
   -------------------------------------------------------------------------------------------------------------------------------------------------
   9000    Ueber den Dächern Berlins Zentrum    POINT(13.334436985013 52.4979779501538)    10777             POLYGON((13.3318284987227 52.4956021172799,...
   ```

1. Calculate the maximum, minimum, or median price of accommodations by using a subquery\. 

   The following query lists the median price of accommodations by postal code\. 

   ```
   SELECT 
     a.price, a.name, ST_AsText(a.shape), 
     z.spatial_name, ST_AsText(z.wkb_geometry) 
   FROM accommodations a, zipcode z 
   WHERE 
     ST_Within(a.shape, z.wkb_geometry) AND 
     price = (SELECT median(price) FROM accommodations)
   ORDER BY a.price;
   ```

   ```
   price name                            st_astext                                   spatial_name   st_astext
   ---------------------------------------------------------------------------------------------------------------------------------------------                    
   45    "Cozy room Berlin-Mitte"        POINT(13.3864349535358 52.5292016386514)    10115          POLYGON((13.3658598465795 52.535659581048,...
   ...
   (723 rows returned)
   ```

1. Run the following query to retrieve the number of accommodations listed in Berlin\. To find the hot spots, these are grouped by postal code and sorted by the amount of supply\. 

   ```
   SELECT z.spatial_name as zip, count(*) as numAccommodations 
   FROM public.accommodations a, public.zipcode z
   WHERE ST_Within(a.shape, z.wkb_geometry)
   GROUP BY zip 
   ORDER BY numAccommodations DESC;
   ```

   ```
   zip  	numaccommodations
   ----------------------------
   10245	872
   10247	832
   10437	733
   10115	664
   ...
   (187 rows returned)
   ```

## Step 3: Clean up your resources<a name="spatial-tutorial-clean-up"></a>

Your cluster continues to accrue charges as long as it's running\. When you have completed this tutorial, you can delete your sample cluster\. 

If you want to keep the cluster but recover the storage used by the test data tables, run the following commands to delete the tables\. 

```
drop table public.accommodations cascade;
```

```
drop table public.zipcode cascade;
```