# Amazon VPC Options<a name="emr-clusters-in-a-vpc"></a>

When launching an EMR cluster within a VPC, you can launch it within either a public or private subnet\. There are slight, notable differences in configuration, depending on the subnet type you choose for a cluster\.

## Public Subnets<a name="emr-vpc-public-subnet"></a>

EMR clusters in a public subnet require a connected internet gateway\. This is because Amazon EMR clusters must access AWS services and Amazon EMR\. If a service, such as Amazon S3, provides the ability to create a VPC endpoint, you can access those services using the endpoint instead of accessing a public endpoint through an internet gateway\. Additionally, Amazon EMR cannot communicate with clusters in public subnets through a network address translation \(NAT\) device\. An internet gateway is required for this purpose but you can still use a NAT instance or gateway for other traffic in more complex scenarios\.

If you have additional AWS resources that you do not want connected to the internet gateway, you can launch those components in a private subnet that you create within your VPC\. 

Clusters running in a public subnet use two security groups, ElasticMapReduce\-master and ElasticMapReduce\-slave, which control access to the master and slave instance groups, respectively\. 


**Public Subnet Security Groups**  

| Security Group Name | Description | Open Inbound Ports | Open Outbound Ports | 
| --- | --- | --- | --- | 
| ElasticMapReduce\-master | Security group for master instance groups of clusters in a public subnet\. | TCP0\-65535844322UDP0\-65535 | All | 
| ElasticMapReduce\-slave | Security group for slave instance groups \(containing core and task nodes\) of clusters in a public subnet\.  | TCP0\-65535UDP0\-65535 | All | 

The master instance group contains the master node while a slave group contains both task and core nodes of the cluster\. All instances in a cluster connect to Amazon S3 through either a VPC endpoint or internet gateway\. Other AWS services which do not currently support VPC endpoints use only an internet gateway\.

The following diagram shows how an Amazon EMR cluster runs in a VPC using a public subnet\. The cluster is able to connect to other AWS resources, such as Amazon S3 buckets, through the internet gateway\.

![\[Cluster on a VPC\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_default_v3a.png)

The following diagram shows how to set up a VPC so that a cluster in the VPC can access resources in your own network, such as an Oracle database\.

![\[Set up a VPC and cluster to access local VPN resources\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_withVPN_v3a.png)

## Private Subnets<a name="emr-vpc-private-subnet"></a>

Private subnets allow you to launch AWS resources without requiring the subnet to have an attached internet gateway\. This might be useful, for example, in an application that uses these private resources in the backend\. Those resources can then initiate outbound traffic using a NAT instance located in another subnet that has an internet gateway attached\. For more information about this scenario, see [Scenario 2: VPC with Public and Private Subnets \(NAT\)](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Scenario2.html)\. 

**Important**  
Amazon EMR only supports launching clusters in private subnets in releases 4\.2 or later\.

The following are differences from public subnets:

+ To access AWS services that do not provide a VPC endpoint, you still must use a NAT instance or an internet gateway\. Currently, the only service supported with a VPC endpoint is Amazon S3\.

+ At a minimum, you must provide a route to the Amazon EMR service logs bucket and Amazon Linux repository in Amazon S3\. For more information, see [Minimum Amazon S3 Policy for Private Subnet](private-subnet-iampolicy.md)

+ If you use EMRFS features, you need to have an Amazon S3 VPC endpoint and a route from your private subnet to DynamoDB\.

+ Debugging only works if you provide a route from your private subnet to a public Amazon SQS endpoint\.

+ Creating a private subnet configuration with a NAT instance or gateway in a public subnet is only supported using the AWS Management Console\. The easiest way to add and configure NAT instances and Amazon S3 VPC endpoints for EMR clusters is to use the **VPC Subnets List** page in the Amazon EMR console\. To configure NAT gateways, follow the procedures outlined in the section called *NAT Gateways* in the [Amazon Virtual Private Cloud User Guide](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-nat-gateway.html)\.

+ You cannot change a subnet with an existing EMR cluster from public to private or vice versa\. To locate an EMR cluster within a private subnet, the cluster must be started in that private subnet\. 

Amazon EMR creates different security groups for the clusters in a private subnet: ElasticMapReduce\-Master\-Private, ElasticMapReduce\-Slave\-Private, and ElasticMapReduce\-ServiceAccess\. 


**Private Subnet Security Groups**  

| Security Group Name | Description | Open Inbound Ports | Open Outbound Ports | 
| --- | --- | --- | --- | 
| ElasticMapReduce\-Master\-Private | Security group for master instance groups of clusters in a private subnet\. | TCP0\-655358443UDP0\-65535 | All | 
| ElasticMapReduce\-Slave\-Private | Security group for slave instance groups \(containing core and task nodes\) of clusters in a private subnet\.  | TCP0\-655358443UDP0\-65535 | All | 
| ElasticMapReduce\-ServiceAccess | Security group for Amazon EMR\-managed elastic network interface resources used to allow communication from the web service to the cluster\. The elastic network interface is owned by you but managed by Amazon EMR\.  | N/A | 8443 | 

For a complete listing of NACLs of your cluster, choose **Security groups for Master** and **Security groups for Core & Task** on the Amazon EMR console **Cluster Details** page\.

The following image shows how an EMR cluster is configured within a private subnet\. The only communication outside the subnet is to Amazon EMR\. 

![\[Launch an EMR cluster in a private subnet\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_with_private_subnet_v3a.png)

The following image shows a sample configuration for an EMR cluster within a private subnet connected to a NAT instance that is residing in a public subnet\.

![\[Private subnet with NAT\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_private_subnet_nat_v3a.png)