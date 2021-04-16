# Managing the volume of merged rows<a name="vacuum-managing-volume-of-unmerged-rows"></a>

If a vacuum operation needs to merge new rows into a table's sorted region, the time required for a vacuum will increase as the table grows larger\. You can improve vacuum performance by reducing the number of rows that must be merged\. 

Prior to a vacuum, a table consists of a sorted region at the head of the table, followed by an unsorted region, which grows whenever rows are added or updated\. When a set of rows is added by a COPY operation, the new set of rows is sorted on the sort key as it is added to the unsorted region at the end of the table\. The new rows are ordered within their own set, but not within the unsorted region\. 

The following diagram illustrates the unsorted region after two successive COPY operations, where the sort key is CUSTID\. For simplicity, this example shows a compound sort key, but the same principles apply to interleaved sort keys, except that the impact of the unsorted region is greater for interleaved tables\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/vacuum-unsorted-region.png)

A vacuum restores the table's sort order in two stages:

1. Sort the unsorted region into a newly\-sorted region\. 

   The first stage is relatively cheap, because only the unsorted region is rewritten\. If the range of sort key values of the newly\-sorted region is higher than the existing range, only the new rows need to be rewritten, and the vacuum is complete\. For example, if the sorted region contains ID values 1 to 500 and subsequent copy operations add key values greater than 500, then only the unsorted region only needs to be rewritten\. 

1. Merge the newly\-sorted region with the previously\-sorted region\. 

   If the keys in the newly sorted region overlap the keys in the sorted region, then VACUUM needs to merge the rows\. Starting at the beginning of the newly\-sorted region \(at the lowest sort key\), the vacuum writes the merged rows from the previously sorted region and the newly sorted region into a new set of blocks\. 

The extent to which the new sort key range overlaps the existing sort keys determines the extent to which the previously\-sorted region will need to be rewritten\. If the unsorted keys are scattered throughout the existing sort range, a vacuum might need to rewrite existing portions of the table\. 

The following diagram shows how a vacuum would sort and merge rows that are added to a table where CUSTID is the sort key\. Because each copy operation adds a new set of rows with key values that overlap the existing keys, almost the entire table needs to be rewritten\. The diagram shows single sort and merge, but in practice, a large vacuum consists of a series of incremental sort and merge steps\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/vacuum-unsorted-region-sort-merge.png)

If the range of sort keys in a set of new rows overlaps the range of existing keys, the cost of the merge stage continues to grow in proportion to the table size as the table grows while the cost of the sort stage remains proportional to the size of the unsorted region\. In such a case, the cost of the merge stage overshadows the cost of the sort stage, as the following diagram shows\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/vacuum-example-merge-region-grows.png)

To determine what proportion of a table was remerged, query SVV\_VACUUM\_SUMMARY after the vacuum operation completes\. The following query shows the effect of six successive vacuums as CUSTSALES grew larger over time\.

```
select * from svv_vacuum_summary
where table_name = 'custsales';

 table_name | xid  | sort_      | merge_     | elapsed_   | row_  | sortedrow_ | block_  | max_merge_
            |      | partitions | increments | time       | delta | delta      | delta   | partitions
 -----------+------+------------+------------+------------+-------+------------+---------+---------------
  custsales | 7072 |          3 |          2 |  143918314 |     0 |   88297472 |   1524  |      47
  custsales | 7122 |          3 |          3 |  164157882 |     0 |   88297472 |    772  |      47
  custsales | 7212 |          3 |          4 |  187433171 |     0 |   88297472 |    767  |      47
  custsales | 7289 |          3 |          4 |  255482945 |     0 |   88297472 |    770  |      47
  custsales | 7420 |          3 |          5 |  316583833 |     0 |   88297472 |    769  |      47
  custsales | 9007 |          3 |          6 |  306685472 |     0 |   88297472 |    772  |      47
 (6 rows)
```

The merge\_increments column gives an indication of the amount of data that was merged for each vacuum operation\. If the number of merge increments over consecutive vacuums increases in proportion to the growth in table size, that is an indication that each vacuum operation is remerging an increasing number of rows in the table because the existing and newly sorted regions overlap\. 