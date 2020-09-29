# Distribution styles<a name="c_choosing_dist_sort"></a>

 When you create a table, you can designate one of four distribution styles; AUTO, EVEN, KEY, or ALL\. 

If you don't specify a distribution style, Amazon Redshift uses AUTO distribution\.

 **AUTO distribution** 

With AUTO distribution, Amazon Redshift assigns an optimal distribution style based on the size of the table data\. For example, Amazon Redshift initially assigns ALL distribution to a small table, then changes to EVEN distribution when the table grows larger\. When a table is changed from ALL to EVEN distribution, storage utilization might change slightly\. The change in distribution occurs in the background, in a few seconds\. 

When you set DISTSTYLE to AUTO, Amazon Redshift might change the distribution of your table data to have a KEY\-based distribution style\. To view actions that Amazon Redshift automatically performed to alter a table distribution key, see [SVL\_AUTO\_WORKER\_ACTION](r_SVL_AUTO_WORKER_ACTION.md)\. To view current recommendations regarding altering a table distribution key, see [SVV\_ALTER\_TABLE\_RECOMMENDATIONS](r_SVV_ALTER_TABLE_RECOMMENDATIONS.md)\. 

To view the distribution style applied to a table, query the PG\_CLASS\_INFO system catalog view\. For more information, see [Viewing distribution styles](viewing-distribution-styles.md)\. If you don't specify a distribution style with the CREATE TABLE statement, Amazon Redshift applies AUTO distribution\. 

 **EVEN distribution** 

 The leader node distributes the rows across the slices in a round\-robin fashion, regardless of the values in any particular column\. EVEN distribution is appropriate when a table does not participate in joins or when there is not a clear choice between KEY distribution and ALL distribution\.

 **KEY distribution** 

 The rows are distributed according to the values in one column\. The leader node places matching values on the same node slice\. If you distribute a pair of tables on the joining keys, the leader node collocates the rows on the slices according to the values in the joining columns so that matching values from the common columns are physically stored together\. 

 **ALL distribution** 

 A copy of the entire table is distributed to every node\. Where EVEN distribution or KEY distribution place only a portion of a table's rows on each node, ALL distribution ensures that every row is collocated for every join that the table participates in\. 

 ALL distribution multiplies the storage required by the number of nodes in the cluster, and so it takes much longer to load, update, or insert data into multiple tables\. ALL distribution is appropriate only for relatively slow moving tables; that is, tables that are not updated frequently or extensively\. Because the cost of redistributing small tables during a query is low, there isn't a significant benefit to define small dimension tables as DISTSTYLE ALL\.  

**Note**  
 After you have specified a distribution style for a column, Amazon Redshift handles data distribution at the cluster level\. Amazon Redshift does not require or support the concept of partitioning data within database objects\. You do not need to create table spaces or define partitioning schemes for tables\. 

In certain scenarios, you can change the distribution style of a table after it is created\. For more information, see [ALTER TABLE](r_ALTER_TABLE.md)\. For scenarios when you can't change the distribution style of a table after it's created, you can recreate the table and populate the new table with a deep copy\. For more information, see [Performing a deep copy](performing-a-deep-copy.md)