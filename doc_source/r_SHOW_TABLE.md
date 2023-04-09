# SHOW TABLE<a name="r_SHOW_TABLE"></a>

Shows the definition of a table, including table attributes, table constraints, column attributes, and column constraints\. You can use the output of the SHOW TABLE statement to recreate the table\. 

For more information on table creation, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\. 

## Syntax<a name="r_SHOW_TABLE-synopsis"></a>

```
SHOW TABLE [schema_name.]table_name 
```

## Parameters<a name="r_SHOW_TABLE-parameters"></a>

 *schema\_name*   
\(Optional\) The name of the related schema\. 

 *table\_name*   
The name of the table to show\. 

## Examples<a name="r_SHOW_TABLE-examples"></a>

Following is an example of the SHOW TABLE output for the table `sales`\.

```
show table sales;
```

```
CREATE TABLE public.sales (
salesid integer NOT NULL ENCODE az64,
listid integer NOT NULL ENCODE az64 distkey,
sellerid integer NOT NULL ENCODE az64,
buyerid integer NOT NULL ENCODE az64,
eventid integer NOT NULL ENCODE az64,
dateid smallint NOT NULL,
qtysold smallint NOT NULL ENCODE az64,
pricepaid numeric(8,2) ENCODE az64,
commission numeric(8,2) ENCODE az64,
saletime timestamp without time zone ENCODE az64
)
DISTSTYLE KEY SORTKEY ( dateid );
```

Following is an example of the SHOW TABLE output for the table `category` in the schema `public`\.

```
show table public.category;
```

```
CREATE TABLE public.category (
catid smallint NOT NULL distkey,
catgroup character varying(10) ENCODE lzo,
catname character varying(10) ENCODE lzo,
catdesc character varying(50) ENCODE lzo
) DISTSTYLE KEY SORTKEY ( catid );
```

The following example creates table `foo` with a primary key\.

```
create table foo(a int PRIMARY KEY, b int);
```

The SHOW TABLE results display the create statement with all properties of the `foo` table\.

```
show table foo;
```

```
CREATE TABLE public.foo ( a integer NOT NULL ENCODE az64, b integer ENCODE az64, PRIMARY KEY (a) ) DISTSTYLE AUTO;
```