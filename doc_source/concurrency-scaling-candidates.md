# Concurrency scaling candidates<a name="concurrency-scaling-candidates"></a>

Queries are routed to the concurrency scaling cluster only when the main cluster meets the following requirements:
+ EC2\-VPC platform\. 
+ Node type must be dc2\.8xlarge, ds2\.8xlarge, dc2\.large, ds2\.xlarge, ra3\.xlplus, ra3\.4xlarge, or ra3\.16xlarge\. Concurrency scaling for write operations is supported on only the following Amazon Redshift RA3 nodes: ra3\.16xlarge, ra3\.4xlarge, and ra3\.xlplus\.
+ Maximum of 32 compute nodes for clusters with ra3\.xlplus, 8xlarge, or 16xlarge node types\. In addition, the number of nodes of the main cluster can't be larger than 32 nodes when the cluster was originally created\. For example, even if a cluster currently has 20 nodes, but was originally created with 40, it does not meet the requirements for concurrency scaling\. Conversely, if a cluster currently has 40 nodes, but was originally created with 20, it does meet the requirements for concurrency scaling\.
+ Not a single\-node cluster\. 