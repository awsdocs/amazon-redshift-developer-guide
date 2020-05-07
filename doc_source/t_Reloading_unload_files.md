# Reloading unloaded data<a name="t_Reloading_unload_files"></a>

To reload the results of an unload operation, you can use a COPY command\.

The following example shows a simple case in which the VENUE table is unloaded using a manifest file, truncated, and reloaded\. 

```
unload  ('select * from venue order by venueid')
to 's3://mybucket/tickit/venue/reload_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
manifest 
delimiter '|';

truncate venue;

copy venue 
from 's3://mybucket/tickit/venue/reload_manifest' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest 
delimiter '|';
```

After it is reloaded, the VENUE table looks like this: 

```
select * from venue order by venueid limit 5;

 venueid |         venuename         |  venuecity  | venuestate | venueseats
---------+---------------------------+-------------+------------+-----------
       1 | Toyota Park               | Bridgeview  | IL         |          0
       2 | Columbus Crew Stadium     | Columbus    | OH         |          0
       3 | RFK Stadium               | Washington  | DC         |          0
       4 | CommunityAmerica Ballpark | Kansas City | KS         |          0
       5 | Gillette Stadium          | Foxborough  | MA         |      68756
(5 rows)
```