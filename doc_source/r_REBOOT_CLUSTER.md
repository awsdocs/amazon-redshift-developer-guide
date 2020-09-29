# REBOOT\_CLUSTER<a name="r_REBOOT_CLUSTER"></a>

Reboot the Amazon Redshift cluster without closing the connections to the cluster\. You must be a database superuser to run this command\.

After this soft reboot has completed, the Amazon Redshift cluster returns an error to the user application and requires the user application to resubmit any transactions or queries interrupted by the soft reboot\.

## Syntax<a name="r_REBOOT_CLUSTER-synopsis"></a>

```
select reboot_cluster();
```