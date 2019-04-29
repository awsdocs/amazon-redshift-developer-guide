# Section 2: Modifying the WLM Query Queue Configuration<a name="tutorial-wlm-modifying-wlm-configuration"></a>

Now that you understand how queues work by default, you can learn how to configure query queues using manual WLM\. In this section, you create and configure a new parameter group for your cluster\. You create two additional user queues and configure them to accept queries based on the queries’ user group or query group labels\. Any queries that don't get routed to one of these two queues are routed to the default queue at run time\.

## Step 1: Create a Parameter Group<a name="tutorial-wlm-create-parameter-group"></a>

In this step, you create a new parameter group to use to configure WLM for this tutorial\. 

### To Create a Parameter Group<a name="how-to-wlm-create-parameter-group"></a>

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Workload management**\.

1. Choose **Create parameter group**\.

1. In the **Create Cluster Parameter Group** dialog box, enter `wlmtutorial` for **Parameter group name** and enter `WLM tutorial` for **Description**\. You can leave the **Parameter group family** setting as is\. Then choose **Create**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_create_cluster_param_group.png)

## Step 2: Configure WLM<a name="tutorial-wlm-configure-wlm"></a>

In this step, you modify the default settings of your new parameter group\. You add two new query queues to the WLM configuration and specify different settings for each queue\.

### To Modify Parameter Group Settings<a name="how-to-wlm-configure-wlm"></a>

1. On the **Parameter Groups** page of the Amazon Redshift console, choose `wlmtutorial`\. Doing this opens the **Parameters** page for `wlmtutorial`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_param_group_list.png)

1. Choose **Switch WLM mode**\. On the **WLM settings** page, choose **Manual WLM** and **Save**\. 

1. Choose the **Workload Management** tab\. Choose **Add queue** twice to add two new queues to this WLM configuration\. Configure the queues with the following values:
   + For queue 1, enter `2` for **Concurrency on main**, `test` for **Query groups**, and `30` for **Memory \(%\)**\. Leave the other settings with their default values\.
   + For queue 2, enter `3` for **Concurrency on main**, `admin` for **User groups**, and `40` for **Memory \(%\)**\. Leave the other settings with their default values\.
   + Don't make any changes to the **Default queue**\. WLM assigns unallocated memory to the default queue\. 

1. Choose **Save**\.

## Step 3: Associate the Parameter Group with Your Cluster<a name="tutorial-wlm-associate-param-group"></a>

In this step, you open your sample cluster and associate it with the new parameter group\. After you do this, you reboot the cluster so that Amazon Redshift can apply the new settings to the database\.

### To Associate the Parameter Group with Your Cluster<a name="how-to-wlm-associate-param-group"></a>

1. In the navigation pane, choose **Clusters**, and then click your cluster to open it\. If you are using the same cluster from *Amazon Redshift Getting Started*, your cluster is named `examplecluster`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster.png)

1. On the **Configuration** tab, choose **Modify** for **Cluster**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_cluster_menu_modify.png)

1. In the **Modify Cluster** dialog box, choose `wlmtutorial` for **Cluster Parameter Group**, and then choose **Modify**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_modify.png)

   The statuses shown in the **Cluster Parameter Group** and **Parameter Group Apply Status** change from **in\-sync** to **applying** as shown in the following\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_modify_applying.png)

   After the new parameter group is applied to the cluster, the **Cluster Properties** and **Cluster Status** show the new parameter group that you associated with the cluster\. You need to reboot the cluster so that these settings can be applied to the database also\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_pending_reboot.png)

1. For **Cluster**, choose **Reboot**\. The status shown in **Cluster Status** changes from **available** to **rebooting**\. After the cluster is rebooted, the status returns to **available**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/console_clusters_examplecluster_cluster_menu_reboot.png)