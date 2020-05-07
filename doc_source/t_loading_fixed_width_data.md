# Loading fixed\-width data from Amazon S3<a name="t_loading_fixed_width_data"></a>

Fixed\-width data files have uniform lengths for each column of data\. Each field in a fixed\-width data file has exactly the same length and position\. For character data \(CHAR and VARCHAR\) in a fixed\-width data file, you must include leading or trailing spaces as placeholders in order to keep the width uniform\. For integers, you must use leading zeros as placeholders\. A fixed\-width data file has no delimiter to separate columns\.

To load a fixed\-width data file into an existing table, USE the FIXEDWIDTH parameter in the COPY command\. Your table specifications must match the value of fixedwidth\_spec in order for the data to load correctly\.

To load fixed\-width data from a file to a table, issue the following command:

```
copy table_name from 's3://mybucket/prefix' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
fixedwidth 'fixedwidth_spec';
```

The *fixedwidth\_spec* parameter is a string that contains an identifier for each column and the width of each column, separated by a colon\. The **column:width** pairs are delimited by commas\. The identifier can be anything that you choose: numbers, letters, or a combination of the two\. The identifier has no relation to the table itself, so the specification must contain the columns in the same order as the table\.

The following two examples show the same specification, with the first using numeric identifiers and the second using string identifiers:

```
'0:3,1:25,2:12,3:2,4:6'
```

```
'venueid:3,venuename:25,venuecity:12,venuestate:2,venueseats:6'
```

The following example shows fixed\-width sample data that could be loaded into the VENUE table using the above specifications:

```
1  Toyota Park               Bridgeview  IL0
2  Columbus Crew Stadium     Columbus    OH0
3  RFK Stadium               Washington  DC0
4  CommunityAmerica Ballpark Kansas City KS0
5  Gillette Stadium          Foxborough  MA68756
```

The following COPY command loads this data set into the VENUE table:

```
copy venue
from 's3://mybucket/data/venue_fw.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
fixedwidth 'venueid:3,venuename:25,venuecity:12,venuestate:2,venueseats:6';
```