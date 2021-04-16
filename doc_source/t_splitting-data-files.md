# Splitting your data into multiple files<a name="t_splitting-data-files"></a>

You can load table data from a single file, or you can split the data for each table into multiple files\. The COPY command can load data from multiple files in parallel\. You can load multiple files by specifying a common prefix, or *prefix key*, for the set, or by explicitly listing the files in a manifest file\.

**Note**  
We strongly recommend that you divide your data into multiple files to take advantage of parallel processing\.

Split your data into files so that the number of files is a multiple of the number of slices in your cluster\. That way Amazon Redshift can divide the data evenly among the slices\. The number of slices per node depends on the node size of the cluster\. For example, each DS2\.XL compute node has two slices, and each DS2\.8XL compute node has 32 slices\. For more information about the number of slices that each node size has, go to [About Clusters and Nodes](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide*\. 

The nodes all participate in parallel query execution, working on data that is distributed as evenly as possible across the slices\. If you have a cluster with two DS2\.XL nodes, you might split your data into four files or some multiple of four\. Amazon Redshift does not take file size into account when dividing the workload, so you need to ensure that the files are roughly the same size, between 1 MB and 1 GB after compression\. 

If you intend to use object prefixes to identify the load files, name each file with a common prefix\. For example, the `venue.txt` file might be split into four files, as follows:

```
venue.txt.1
venue.txt.2
venue.txt.3
venue.txt.4
```

If you put multiple files in a folder in your bucket, you can specify the folder name as the prefix and COPY will load all of the files in the folder\. If you explicitly list the files to be loaded by using a manifest file, the files can reside in different buckets or folders\.

For more information about manifest files, see [Using a manifest to specify data files](r_COPY_command_examples.md#copy-command-examples-manifest)\.