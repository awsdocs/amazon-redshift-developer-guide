# Section 2: Modifying the WLM Query Queue Configuration<a name="tutorial-wlm-modifying-wlm-configuration"></a>

Now that you understand how queues work by default, you will learn how to configure query queues in WLM\. In this section, you’ll create and configure a new parameter group for your cluster\. You’ll create two additional user queues and configure them to accept queries based on the queries’ user group or query group labels\. Any queries that do not get routed to one of these two queues will be routed to the default queue at run time\.

## Step 1: Create a Parameter Group<a name="tutorial-wlm-create-parameter-group"></a>

In this step, we’ll create a new parameter group to use to configure WLM for this tutorial\. 

### To Create a Parameter Group<a name="how-to-wlm-create-parameter-group"></a>

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, click **Parameter Groups**\.

1. Click **Create Cluster Parameter Group**\.

1. In the **Create Cluster Parameter Group** dialog box, type `wlmtutorial` in the **Parameter Group Name** box and type `WLM tutorial` in the **Description** box\. You can leave the **Parameter Group Family** setting as is\. Then click **Create**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_create_cluster_param_group.png)

## Step 2: Configure WLM<a name="tutorial-wlm-configure-wlm"></a>

In this step, you’ll modify the default settings of your new parameter group\. You’ll add two new query queues to the WLM configuration and specify different settings for each queue\.

### To Modify Parameter Group Settings<a name="how-to-wlm-configure-wlm"></a>

1. On the **Parameter Groups** page of the Amazon Redshift console, click the magnifying glass icon next to `wlmtutorial`\. Doing this opens up the **Parameters** tab for `wlmtutorial`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_param_group_list.png)

1. On the **Parameters** tab, review the parameters and values\. At this point, all of the settings will have default values\. The **wlm\_json\_configuration** parameter is the one that will be modified when you configure WLM in this tutorial\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_param_group_default_parameters.png)

1. Click the **WLM** tab\. Click **Add New Queue** twice to add two new queues to this parameter group\. Configure the queues with the following values\.

   + For queue 1, type `2` in the **Concurrency** box, `test` in the **Query Groups** box, and `30` in the **% Memory** box\. Leave the other boxes empty\.
**Note**  
You can ignore the warning about invalid memory allocation\. After you specify memory values to total 100 percent for all of the queues, the message will disappear\.

   + For queue 2, type `3` in the **Concurrency** box, `admin` in the **User Groups** box, and `40` in the **% Memory** box\. Leave the other boxes empty\.

   + For queue 3, type `30` in the **% Memory** box\. Leave the other boxes empty\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_param_group_modify_wlm.png)

1. Click **Save Changes**\.

## Step 3: Associate the Parameter Group with Your Cluster<a name="tutorial-wlm-associate-param-group"></a>

In this step, you’ll open your sample cluster and associate it with the new parameter group\. After you do this, you’ll reboot the cluster so that Amazon Redshift can apply the new settings to the database\.

### To Associate the Parameter Group with Your Cluster<a name="how-to-wlm-associate-param-group"></a>

1. In the navigation pane, click **Clusters**, and then click your cluster to open it\. If you are using the same cluster from *Amazon Redshift Getting Started*, your cluster will be named `examplecluster`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster.png)

1. On the **Configuration** tab, click **Modify** in the **Cluster** menu\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_cluster_menu_modify.png)

1. In the **Modify Cluster** dialog box, select `wlmtutorial` from the **Cluster Parameter Group** menu, and then click **Modify**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_modify.png)

   The statuses shown in the **Cluster Parameter Group** and **Parameter Group Apply Status** will change from **in\-sync** to **applying** as shown in the following\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_modify_applying.png)

   After the new parameter group is applied to the cluster, the **Cluster Properties** and **Cluster Status** show the new parameter group that you associated with the cluster\. You need to reboot the cluster so that these settings can be applied to the database also\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_pending_reboot.png)

1. In the **Cluster** menu, click **Reboot**\. The status shown in **Cluster Status** will change from **available** to **rebooting**\. After the cluster is rebooted, the status will return to **available**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_cluster_menu_reboot.png)