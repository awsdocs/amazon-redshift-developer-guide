# activate\_aqua<a name="r_activate_aqua"></a>

## Values<a name="r_activate_aqua-values"></a>

false, true

## Description<a name="description"></a>

A session configuration that modifies whether AQUA is active to process queries\. You can use this session configuration to evaluate and compare AQUA accelerated queries on clusters\. To use this session configuration, AQUA must be turned on at the cluster level\.  When you set `activate_aqua` to `true`, AQUA is activated\. When you set `activate_aqua` to `false`, AQUA is not used\. The default value of the configuration matches the AQUA configuration setting of the cluster at the start of the session\. For more information, see [Working with AQUA](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-cluster-aqua.html) in the *Amazon Redshift Cluster Management Guide*\. 

## Example<a name="r_activate_aqua-example"></a>

```
set activate_aqua to false;
```