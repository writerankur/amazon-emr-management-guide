# Instance Store and Amazon EBS<a name="emr-plan-storage"></a>

There are two types of storage volumes available for EC2 instances: Amazon EBS volumes and the instance store\. Amazon EBS volumes are available only in Amazon EMR version 4\.0 and later\. Whether the root device volume uses the instance store or an Amazon EBS volume depends on the AMI\. Some AMIs are backed by Amazon EC2 instance store, and some are backed by Amazon EBS\. For more information, see [Amazon EC2 Root Device Volume](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/RootDeviceStorage.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Amazon EBS works differently within Amazon EMR than it does with regular Amazon EC2 instances\. Amazon EBS volumes attached to EMR clusters are ephemeral: the volumes are deleted upon cluster and instance termination \(for example, when shrinking instance groups\), so it's important that you not expect data to persist\. Although the data is ephemeral, it is possible that data in HDFS may be replicated depending on the number and specialization of nodes in the cluster\. When you add EBS storage volumes, these are mounted as additional volumes\. They are not a part of the boot volume\. YARN is configured to use all the additional volumes, but you are responsible for allocating the additional volumes as local storage \(for local log files for example\)\.

Instance store and/or EBS volume storage is used for HDFS data, as well as buffers, caches, scratch data, and other temporary content that some applications may "spill" to the local file system\. EMRFS can help ensure that there is a persistent "source of truth" for HDFS data stored in Amazon S3\.

Amazon EMR automatically attaches an Amazon EBS General Purpose SSD \(gp2\) 10\-GB volume as the root device for its AMIs to enhance performance\. The EBS costs are pro\-rated by the hour based on the monthly Amazon EBS charges for gp2 volumes in the region where the cluster runs\. For example, the EBS cost per hour for the root volume on each cluster node in a region that charges $0\.10/GB/month would be approximately $0\.00139 per hour \($0\.10/GB/month divided by 30 days divided by 24h times 10 GB\)\.

When you configure instance types in Amazon EMR, you can specify additional EBS volumes, which adds capacity beyond the instance store \(if present\) and the default EBS volume\. Amazon EBS provides the following volume types: General Purpose \(SSD\), Provisioned IOPS \(SSD\), Throughput Optimized \(HDD\), Cold \(HDD\), and Magnetic\. They differ in performance characteristics and price, so you can tailor your storage based on the analytic and business needs of your applications\. For example, some applications may have a need to spill to disk while others can safely work in\-memory or using Amazon S3\.

You can only attach EBS volumes to instances at cluster startup time unless you add an extra task node instance group, at which time you can add EBS volumes\. If an instance in an EMR cluster fails, then both the instance and attached EBS volumes are replaced as new\. Consequently, if you manually detach an EBS volume, Amazon EMR treats that as a failure and replaces both instance storage \(if applicable\) and the volume stores\.

Other caveats for using Amazon EBS with EMR clusters are:

+ You can't snapshot an EBS volume and then restore it within Amazon EMR\. To create reusable custom configurations, use a custom AMI \(available in Amazon EMR version 5\.7\.0 and later\)\. For more information, see [Using a Custom AMI](emr-custom-ami.md)\.

+ An encrypted EBS root storage volume is supported only when using a custom AMI\. For more information, see [Creating a Custom AMI with an Encrypted Amazon EBS Root Device Volume](emr-custom-ami.md#emr-custom-ami-encrypted)\.\. Encrypted EBS storage volumes are not supported\.

+ If you apply tags using the Amazon EMR API, those operations are applied to EBS volumes\.

+ There is a limit of 25 volumes per instance\.