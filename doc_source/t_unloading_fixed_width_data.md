# Unloading data in delimited or fixed\-width format<a name="t_unloading_fixed_width_data"></a>

You can unload data in delimited format or fixed\-width format\. The default output is pipe\-delimited \(using the '\|' character\)\.

The following example specifies a comma as the delimiter: 

```
unload ('select * from venue')
to 's3://mybucket/tickit/venue/comma' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter ',';
```

The resulting output files look like this: 

```
20,Air Canada Centre,Toronto,ON,0
60,Rexall Place,Edmonton,AB,0
100,U.S. Cellular Field,Chicago,IL,40615
200,Al Hirschfeld Theatre,New York City,NY,0
240,San Jose Repertory Theatre,San Jose,CA,0
300,Kennedy Center Opera House,Washington,DC,0
...
```

To unload the same result set to a tab\-delimited file, issue the following command: 

```
unload ('select * from venue') 
to 's3://mybucket/tickit/venue/tab' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter as '\t';
```

Alternatively, you can use a FIXEDWIDTH specification\. This specification consists of an identifier for each table column and the width of the column \(number of characters\)\. The UNLOAD command will fail rather than truncate data, so specify a width that is at least as long as the longest entry for that column\. Unloading fixed\-width data works similarly to unloading delimited data, except that the resulting output contains no delimiting characters\. For example: 

```
unload ('select * from venue')
to 's3://mybucket/tickit/venue/fw' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
fixedwidth '0:3,1:100,2:30,3:2,4:6';
```

The fixed\-width output looks like this: 

```
20 Air Canada Centre         Toronto      ON0
60 Rexall Place              Edmonton     AB0
100U.S. Cellular Field       Chicago      IL40615
200Al Hirschfeld Theatre     New York CityNY0
240San Jose Repertory TheatreSan Jose     CA0
300Kennedy Center Opera HouseWashington   DC0
```

For more details about FIXEDWIDTH specifications, see the [UNLOAD](r_UNLOAD.md) command\.