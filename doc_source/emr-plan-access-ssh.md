# Use an Amazon EC2 Key Pair for SSH Credentials<a name="emr-plan-access-ssh"></a>

Amazon EMR can use an Amazon EC2 key pair to authorize SSH client connections to cluster instances\. Usually, an SSH connection is used to connect directly to the master node to interact directly with system files and logs, or to view web interfaces that the master instance hosts\. Alternatively, with Amazon EMR release version 5\.10\.0 or later, you can configure Kerberos to authenticate users and SSH connections to the master node\. For more information, see [Use Kerberos Authentication](emr-kerberos.md)\.

You can use Amazon EC2 to create a key pair, or you can import a key pair\. When you create a cluster, you specify the Amazon EC2 key pair to use for SSH connections\. The SSH client that you use to connect needs the private key file associated with this key pair\. This is a \.pem file for SSH clients using Linux, Unix and macOS, and you must set permissions so that only the key owner has permission to access the file\. This is a \.ppk file for SSH clients using Windows, and the \.ppk file is usually created from the \.pem file\.

+ For more information about creating an Amazon EC2 key pair, see [Amazon EC2 Key Pairs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

+ For instructions about using PuTTYgen to create a \.ppk file from a \.pem file, see [Converting Your Private Key Using PuTTYgen](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-private-key) in the *Amazon EC2 User Guide for Linux Instances*\.

+ For more information about setting \.pem file permissions and how to connect to an EMR cluster's master node using different methods—including `ssh` from Linux or macOS, PuTTY from Windows, or the AWS CLI from any supported operating system—see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\.