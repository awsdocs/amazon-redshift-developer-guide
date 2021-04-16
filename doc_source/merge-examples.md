# Merge examples<a name="merge-examples"></a>

The following examples perform a merge to update the SALES table\. The first example uses the simpler method of deleting from the target table and then inserting all of the rows from the staging table\. The second example requires updating on select columns in the target table, so it includes an extra update step\. 

**Sample merge data source**

The examples in this section need a sample data source that includes both updates and inserts\. For the examples, we will create a sample table named SALES\_UPDATE that uses data from the SALES table\. We'll populate the new table with random data that represents new sales activity for December\. We will use the SALES\_UPDATE sample table to create the staging table in the examples that follow\. 

```
-- Create a sample table as a copy of the SALES table
create table sales_update as
select * from sales;

-- Change every fifth row so we have updates

update sales_update
set qtysold = qtysold*2,
pricepaid = pricepaid*0.8,
commission = commission*1.1
where saletime > '2008-11-30'
and mod(sellerid, 5) = 0;

-- Add some new rows so we have insert examples 
-- This example creates a duplicate of every fourth row

insert into sales_update
select (salesid + 172456) as salesid, listid, sellerid, buyerid, eventid, dateid, qtysold, pricepaid, commission, getdate() as saletime
from sales_update
where saletime > '2008-11-30'
and mod(sellerid, 4) = 0;
```

**Example of a merge that replaces existing rows**

The following script uses the SALES\_UPDATE table to perform a merge operation on the SALES table with new data for December sales activity\. This example deletes rows in the SALES table that have updates so they can be replaced with the updated rows in the staging table\. The staging table should contain only rows that will participate in the merge, so the CREATE TABLE statement includes a filter to exclude rows that have not changed\. 

```
-- Create a staging table and populate it with updated rows from SALES_UPDATE 

create temp table stagesales as
select * from sales_update
where sales_update.saletime > '2008-11-30'
and sales_update.salesid = (select sales.salesid from sales
where sales.salesid = sales_update.salesid
and sales.listid = sales_update.listid
and (sales_update.qtysold != sales.qtysold 
or sales_update.pricepaid != sales.pricepaid));

-- Start a new transaction
begin transaction;

-- Delete any rows from SALES that exist in STAGESALES, because they are updates
-- The join includes a redundant predicate to collocate on the distribution key 
–- A filter on saletime enables a range-restricted scan on SALES

delete from sales
using stagesales
where sales.salesid = stagesales.salesid
and sales.listid = stagesales.listid
and sales.saletime > '2008-11-30';

-- Insert all the rows from the staging table into the target table
insert into sales
select * from stagesales;

-- End transaction and commit
end transaction;

-- Drop the staging table
drop table stagesales;
```

**Example of a merge that specifies a column list**

The following example performs a merge operation to update SALES with new data for December sales activity\. We need sample data that includes both updates and inserts, along with rows that have not changed\. For this example, we want to update the QTYSOLD and PRICEPAID columns but leave COMMISSION and SALETIME unchanged\. The following script uses the SALES\_UPDATE table to perform a merge operation on the SALES table\. 

```
-- Create a staging table and populate it with rows from SALES_UPDATE for Dec
create temp table stagesales as select * from sales_update
where saletime > '2008-11-30';

-- Start a new transaction
begin transaction;

-- Update the target table using an inner join with the staging table
-- The join includes a redundant predicate to collocate on the distribution key –- A filter on saletime enables a range-restricted scan on SALES

update sales
set qtysold = stagesales.qtysold,
pricepaid = stagesales.pricepaid
from stagesales
where sales.salesid = stagesales.salesid
and sales.listid = stagesales.listid
and stagesales.saletime > '2008-11-30'
and (sales.qtysold != stagesales.qtysold 
or sales.pricepaid != stagesales.pricepaid);
 
-- Delete matching rows from the staging table 
-- using an inner join with the target table

delete from stagesales
using sales
where sales.salesid = stagesales.salesid
and sales.listid = stagesales.listid;

-- Insert the remaining rows from the staging table into the target table
insert into sales
select * from stagesales;

-- End transaction and commit
end transaction;

-- Drop the staging table
drop table stagesales;
```