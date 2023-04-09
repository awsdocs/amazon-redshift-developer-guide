# SHOW VIEW<a name="r_SHOW_VIEW"></a>

Shows the definition of a view, including for materialized views and late\-binding views\. You can use the output of the SHOW VIEW statement to recreate the view\. 

## Syntax<a name="r_SHOW_VIEW-synopsis"></a>

```
SHOW VIEW [schema_name.]view_name 
```

## Parameters<a name="r_SHOW_VIEW-parameters"></a>

 *schema\_name*   
\(Optional\) The name of the related schema\. 

 *view\_name*   
The name of the view to show\. 

## Examples<a name="r_SHOW_VIEW-examples"></a>

 Following is the view definition for the view `LA_Venues_v`\.

```
create view LA_Venues_v as select * from venue where venuecity='Los Angeles';
```

Following is an example of the SHOW VIEW command and output for the view defined preceding\.

```
show view LA_Venues_v;
```

```
SELECT venue.venueid,
venue.venuename,
venue.venuecity,
venue.venuestate,
venue.venueseats
FROM venue WHERE ((venue.venuecity)::text = 'Los Angeles'::text);
```

Following is the view definition for the view `public.Sports_v` in the schema `public`\.

```
create view public.Sports_v as select * from category where catgroup='Sports';
```

Following is an example of the SHOW VIEW command and output for the view defined preceding\.

```
show view public.Sports_v;
```

```
SELECT category.catid,
category.catgroup,
category.catname,
category.catdesc
FROM category WHERE ((category.catgroup)::text = 'Sports'::text);
```