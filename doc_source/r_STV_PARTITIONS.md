# STV\_PARTITIONS<a name="r_STV_PARTITIONS"></a>

Use the STV\_PARTITIONS table to find out the disk speed performance and disk utilization for Amazon Redshift\.

STV\_PARTITIONS contains one row per node per logical disk partition, or slice\.

STV\_PARTITIONS is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_PARTITIONS-table-rows2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_PARTITIONS.html)

## Sample query<a name="r_STV_PARTITIONS-sample-query2"></a>

The following query returns the disk space used and capacity, in 1 MB disk blocks, and calculates disk utilization as a percentage of raw disk space\. The raw disk space includes space that is reserved by Amazon Redshift for internal use, so it is larger than the nominal disk capacity, which is the amount of disk space available to the user\. The **Percentage of Disk Space Used** metric on the **Performance** tab of the Amazon Redshift Management Console reports the percentage of nominal disk capacity used by your cluster\. We recommend that you monitor the **Percentage of Disk Space Used** metric to maintain your usage within your cluster's nominal disk capacity\. 

**Important**  
We strongly recommend that you do not exceed your cluster's nominal disk capacity\. While it might be technically possible under certain circumstances, exceeding your nominal disk capacity decreases your cluster's fault tolerance and increases your risk of losing data\.

This example was run on a two\-node cluster with six logical disk partitions per node\. Space is being used very evenly across the disks, with approximately 25% of each disk in use\. 

```
select owner, host, diskno, used, capacity,
(used-tossed)/capacity::numeric *100 as pctused 
from stv_partitions order by owner;

 owner | host | diskno |  used  | capacity | pctused
-------+------+--------+--------+----------+---------
   0   |  0   |    0   | 236480 |  949954  | 24.9
   0   |  0   |    1   | 236420 |  949954  | 24.9
   0   |  0   |    2   | 236440 |  949954  | 24.9
   0   |  1   |    2   | 235150 |  949954  | 24.8
   0   |  1   |    1   | 237100 |  949954  | 25.0
   0   |  1   |    0   | 237090 |  949954  | 25.0
   1   |  1   |    0   | 236310 |  949954  | 24.9
   1   |  1   |    1   | 236300 |  949954  | 24.9
   1   |  1   |    2   | 236320 |  949954  | 24.9
   1   |  0   |    2   | 237910 |  949954  | 25.0
   1   |  0   |    1   | 235640 |  949954  | 24.8
   1   |  0   |    0   | 235380 |  949954  | 24.8 

(12 rows)
```