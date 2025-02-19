---

copyright:
  years: 2019
lastupdated: "2019-07-29"

keywords: vpc, cli, command line interface, tutorial, creating a vpc

subcollection: vpc


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:note: .note}
{:tip: .tip}
{:table: .aria-labeledby="caption"}
{:download: .download}

# Creating a VPC using the CLI
{: #creating-a-vpc-using-cli}

This guide shows you how to create and configure an {{site.data.keyword.vpc_full}} using the {{site.data.keyword.cloud}} CLI.
{:shortdesc}

To create and configure your virtual private cloud (VPC) and other attached resources, perform the steps in the sections that follow, in this order:

1. Create a VPC and subnet to define the network.
1. Attach a public gateway to allow all resources in the subnet to communicate with the public internet. 
1. Create a virtual server instance.
1. Create a block storage volume and attach it to an instance.
1. Configure a security group to define the inbound and outbound traffic that's allowed for the instance.
1. Reserve and associate a floating IP address to enable your instance to be reachable from the internet.

## Before you begin
{: #before-cli-tutorial}
Make sure you [set up your environment](/docs/vpc?topic=vpc-set-up-environment).

## Log in to IBM Cloud
{: #log-in-to-ibm-cloud}

```
ibmcloud login --sso -a cloud.ibm.com
 ```
{: pre}

This command returns a URL and prompts for a passcode. Go to that URL in your browser and log in. If successful, you will get a one-time passcode. Copy this passcode and paste it as a response on the prompt. After the authentication steps, you'll be prompted to choose an account. Choose the account that was granted access to participate in the early access program. If you have access to multiple users, select the user you want to log in as. 

When you are prompted to select a region, select us-south.

Respond to any remaining prompts to finish logging in.

## Select the generation of VPC infrastructure services
{: #select-the-generation }

Use the following command to configure the CLI plugin to target next generation services for VPC (early access).

```
ibmcloud is target --gen 2
```
{: pre}

## Create a VPC
{: #create-a-vpc-cli}

Use the following command to create a VPC named _my-vpc_.

```
ibmcloud is vpc-create my-vpc
```
{: pre}

From the output that's returned, save the ID in a variable so you can use it later, for example:

```
vpc="59de4046-3434-4d87-bb29-0c99c428c96e"
```
{: pre}

## Create a subnet
{: #create-a-subnet-cli}

Before you create a subnet, select the zone and address prefix in which to create it. To list the address prefixes for each zone in your VPC, run the following command:

```
ibmcloud is vpc-address-prefixes $vpc
```
{: pre}

Let's pick the default address prefix for the us-south-3 zone. From the command output, note the CIDR block of the address prefix. When you create a subnet, you must specify an IP range that's within one of the address prefixes of the selected zone. 

```
ibmcloud is subnet-create my-subnet $vpc us-south-3 --ipv4-cidr-block "10.0.1.0/24"
```
{: pre}

From the output that's returned, save the ID in a variable so you can use it later, for example:

```
subnet="aaaa_658756a4-1106-4914-969a-3b43b338524a"
```
{: pre}

## Check the status of your subnet
{: #check-subnet-status-cli}

The status of the subnet is `pending` when it's first created. Before you can create resources in the subnet, the subnet needs to move to the `available` status, which takes a few seconds. To check the status of the subnet, run this command:

```
ibmcloud is subnet $subnet
```
{: pre}

## Optional: Attach a public gateway
{: #attach-public-gateway-cli}

Attach a public gateway to the subnet if you want to allow all attached resources to communicate with the public internet. 

To create a public gateway, run the following command:

```
ibmcloud is public-gateway-create my-gateway $vpc us-south-3
```
{: pre}

From the output that's returned, save the ID in a variable so you can use it later, for example:

```
gateway="446c0c63-f0b1-4043-b30d-644f55fde391"
```
{: pre}

To attach the public gateway to your subnet, run the following command:
```
ibmcloud is subnet-update $subnet --public-gateway-id $gateway
```
{: pre}

Only one public gateway per zone is allowed in a VPC, but that public gateway can be attached to multiple subnets in the zone. To find the public gateway for a zone, run the 'ibmcloud is public-gateways` command and look for the particular VPC and Zone values.
{: tip}

## Add an SSH key
{: #add-ssh-key-cli}

Add your public SSH key to your IBM Cloud acccount. This key is speciified when you create the instance, and it's needed later to log into the instance. You can use one key to provision multiple instances.

To see the available keys in your IBM Cloud account, run this command:

```
ibmcloud is keys
```
{: pre}

To add a key, run the following command. Substitute the path to your `id_rsa.pub` file.

```
ibmcloud is key-create my-key @$HOME/.ssh/id_rsa.pub
```
{: pre}

From the output that's returned, save the ID in a variable so you can use it later, for example:

```
key="859b4e97-7540-4337-9c64-384792b85653"
```
{: pre}

## Select a profile and image for the instance
{: #select-a-profile-and-image-for-the-instance}

To list all available instance profiles, run the following command:

```
ibmcloud is instance-profiles
```
{: pre}

To list all available images, run the following command:

```
ibmcloud is images
```
{: pre}

Let's pick instance profile `b2-2x8` and image `debian-9.x-amd64`. To get the image ID, run the following command:

```
image=$(ibmcloud is images | grep -i "debian" | cut -d" " -f1)
```
{: pre}

## Create an instance
{: #create-an-instance}

Create an instance in the newly created subnet. Pass in your public SSH key so that you can log in after the instance is provisioned.

```
ibmcloud is instance-create my-instance $vpc us-south-3 b2-2x8 $subnet --image-id $image --key-ids $key
```
{: pre}

From the output that's returned, save the ID of the instance in a variable so you can use it later, for example:

```
instance="aaaa_21179496-964e-4c00-8210-cf23d75750b3"
```
{:pre}

## Check the status of your instance
{: #check-instance-status-cli}

The status of the instance is `pending` when it's first created. Before you can proceed, the instance needs to move to the `running` status, which takes a few minutes. To check the status of the instance, run this command:

```
ibmcloud is instance $instance
```
{: pre}

From the output that's returned, save the ID of the primary network interface (`Primary Interface`) in a variable so you can use it later, for example:

```
nic="aaaa_4d9b3a58-f796-4e6a-b5ac-84f4216e9b68-glhvl"
```

## Create a block storage data volume
{: #create-block-storage-data-volume-cli}

When you create a block storage volume, you select a profile to optimize the performance of your compute workloads. See [About Block Storage for VPC](/docs/vpc?topic=vpc-block-storage-about#capacity-performance) for information about volume capacity and IOPS ranges based on the volume profile you select.  

To see a list of volume profiles, run:

```
ibmcloud is volume-profiles
```
{: pre}

Run this command to create a block storage data volume. Specify a name for your volume, volume profile, and the zone where you are creating the volume. To attach a block storage data volume to an instance, the instance and the block storage data volume must be created in the same zone.

```
ibmcloud is volume-create my-volume custom us-south-2 --iops 1000
```
{: pre}

From the output that's returned, save the ID of the volume in a variable so you can use it later:

```
vol=933c8781-f7f5-4a8f-8a2d-3bfc711788ee
```
{: pre}

Note that the status of the volume is `pending` when it first is created. Before you can proceed, the volume needs to move to `available` status, which takes a few minutes. 

To check the status of the volume, run this command:

```
ibmcloud is volume $vol
```
{: pre}

## Attach a block storage data volume to an instance
{: #attach-block-storage-data-volume-cli}

Use the following command to attach the volume to the virtual server instance, using the variables we created:

```
ibmcloud is instance-volume-attachment-add my-volume-attachment $instance $vol --auto-delete true
```
{: pre}

## Add rules to the default security group
{: #add-rules-to-default-security-group}

You can configure the security group to define the inbound and outbound traffic that is allowed for the instance. 

Find the security group for the VPC:

```
ibmcloud is vpc-sg $vpc
```
{: pre}

From the output that's returned, save the ID in a variable so you can use it later:

```
sg=2d364f0a-a870-42c3-a554-000000981149
```
{: pre}

Now create a rule to allow SSH traffic:

```
ibmcloud is sg-rulec $sg inbound tcp --port-min=22 --port-max=22
```
{: pre}

Optionally, you can also add a rule to allow ping traffic:

```
ibmcloud is sg-rulec $sg inbound icmp --icmp-type 8 --icmp-code 0
```
{: pre}

## Create a floating IP address for the instance
{: #create-floating-ip-address-cli}

Create a floating IP address to enable your instance to be reachable from the internet.

```
ibmcloud is floating-ip-reserve my-fip --nic-id $nic
```
{: pre}

From the output that's returned, save the `Address` in a variable so you can use it later:

```
address=169.48.88.0
```
{: pre}

## Log in to your instance using your private SSH key
{: #log-in-to-your-instance-using-your-private-ssh-key}

For example, you can use a command of this form:

```
ssh -i $HOME/.ssh/id_rsa root@$address
```
{:pre}

When you're prompted to continue connecting, type `yes`.

SSH access into the instance might be prevented by security groups. Make sure the instance's security group allows SSH access.
{: tip}

## Hello, World!
{: #hello-world-cli}

Run the following command in the terminal window:

```
echo `hostname` says "Hello, World!"
```
{:pre}

You should see the following output:

```
my-instance says Hello, World!
```
{:screen}

## Start using your block storage data volume
{: #start-using-your-block-storage-data-volume}

To use your block storage volume as a filesystem, you'll need to partition the volume, format the volume, and then mount it as a filesystem.

On Linux, run the following command to list all block storage volumes from your instance:

```
lsblk
```
{:pre}

You should see output like this:

```
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  100G  0 disk 
├─xvda1 202:1    0  256M  0 part /boot
└─xvda2 202:2    0 99.8G  0 part /
xvdc    202:32   0  100G  0 disk 
xvdp    202:240  0   64M  0 disk 
```
{:screen}

Volume `xvdc` is your new block storage data volume. 

Run the following command to partition the volume. To begin, use the `n` command for a new partition.

```
fdisk /dev/xvdc
```
{:pre}

Format the volume.

```
/sbin/mkfs -t ext3 /dev/xvdc
```
{:pre}

Label the volume as "myvolume".

```
/sbin/e2label /dev/xvdc /myvolume
```
{:pre}

Create the directory and mount the volume as a filesystem.

```
mkdir /hellovol
mount /dev/xvdc /myvolume
```
{: codeblock}

To see your new filesystem, run the following command:

```
df -k
```
{:pre}

You should see output like this:

```
Filesystem     1K-blocks    Used Available Use% Mounted on
udev             4075344       0   4075344   0% /dev
tmpfs             816936    8844    808092   2% /run
/dev/xvda2     101330012 1261048 100052580   2% /
tmpfs            4084664       0   4084664   0% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs            4084664       0   4084664   0% /sys/fs/cgroup
/dev/xvda1        245679   64360    168212  28% /boot
tmpfs             817040       0    817040   0% /run/user/0
/dev/xvdc      103081248   61176  97777192   1% /myvolume
```
{:screen}

To change directory into your new filesystem and create a file, do something like this:

```
cd /myvolume
touch myvolume
```
{:codeblock}

## Congratulations!
{: #congratulations-cli-tutorials}

You've successfully provisioned and connected to your VPC using the IBM Cloud CLI. To try out more CLI commands, see [CLI reference](/docs/vpc?topic=vpc-cli-reference#cli-reference)
