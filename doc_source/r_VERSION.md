# VERSION<a name="r_VERSION"></a>

 The VERSION function returns details about the currently installed release, with specific Amazon Redshift version information at the end\. 

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\.

## Syntax<a name="r_VERSION-synopsis"></a>

```
VERSION()
```

## Return type<a name="r_VERSION-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Examples<a name="r_VERSION-examples"></a>

The following example shows the cluster version information of the current cluster: 

```
select version();
```

```
 version
 ------------------------------------------------------------------------------------------------------------------------               
 PostgreSQL 8.0.2 on i686-pc-linux-gnu, compiled by GCC gcc (GCC) 3.4.2 20041017 (Red Hat 3.4.2-6.fc3), Redshift 1.0.12103
```

Where `1.0.12103` is the cluster version number\. 

**Note**  
To force your cluster to update to the latest cluster version, adjust your [maintenance window](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-maintenance-windows)\. 