---

copyright:
  years: 2019
lastupdated: "2019-07-29"

keywords: block storage, IBM Cloud, VPC, virtual private cloud, boot volume, data volume, volume, data storage, virtual server instance, instance, IOPS, HPCS, Key Protect

subcollection: vpc

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:important: .important}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}

# About {{site.data.keyword.block_storage_is_short}}
{: #block-storage-about}
[comment]: # (linked help topic)

{{site.data.keyword.block_storage_is_full}} (VPC) provides hypervisor-mounted, high-performance data storage for your virtual server instances (instances) that you can provision within an {{site.data.keyword.vpc_full}} (VPC). The VPC infrastructure provides rapid scaling across multiple regions and zones, and extra performance and security. For more information about {{site.data.keyword.vpc_short}}, see [About Virtual Private Cloud](/docs/vpc?topic=vpc-about-vpc).
{:shortdesc}

{{site.data.keyword.block_storage_is_short}} provides primary boot volumes and secondary data volumes. Boot volumes are automatically created and attached during instance provisioning. Data volumes can be created and attached during instance provisoning as well, or as standalone volumes that you can later attach to an instance. To protect your data, you can use your own encryption key or choose IBM-managed encryption. [IOPS tier profiles](/docs/vpc?topic=vpc-block-storage-profiles#tiers) let you specify a pre-defined level of performance for your volumes. Or, you can choose a [custom IOPS profile](/docs/vpc?topic=vpc-block-storage-profiles#custom) and define your own volume capacity and IOPS level.

## Block storage for VPC volumes
{: #block-storage-vpc-volumes}

{{site.data.keyword.block_storage_is_short}} offers block-level data storage volumes that can be attached to a instance as either a boot volume or as a data volume. You can configure up to 750 block storage volumes per region. You can attach several block storage data volumes to a single instance for extra capacity. The number of volumes you can attach to an instance depends on how many vCPUs that instance contains. For more information, see [Volume attachment limits](/docs/vpc?topic=vpc-attaching-block-storage#vol-attach-limits).

### Boot volumes
{: #block-storage-vpc-boot-volumes}

When you create an instance, a 100 GB boot volume is automatically created and attached to the instance. The boot volume has a maximum IOPS of 3,000 IOPS. Boot volumes are encrypted by IBM-managed encryption. A boot volume can't be manually detached and deleted but it is deleted when you delete the instance.

### Secondary data volumes
{: #secondary-data-volumes}

Block storage data volumes are secondary volumes with total capacity range of 10 GB to 2000 GB. Maximum IOPS for data volumes vary based on volume size and the IOPS tier profile that you selected. For example, the max IOPS for a general purpose volume up to 1 TB is 3,000 IOPS. For more information, see
[Tiered IOPs profiles](/docs/vpc?topic=vpc-block-storage-profiles#tiers).

You create data volumes as standalone volumes or when you provision an instance. Standalone volumes exist in an unattached state until you attach the volume to an instance. When you create a data volume as part of instance provisioning, the volume is automatically attached to the instance.  

Block storage data volumes can be attached to any available instance within your region, within [certain limits](/docs/vpc?topic=vpc-attaching-block-storage#vol-attach-limits). These volumes are detached by default when the instance is deleted. Detaching by default allows your data to persist beyond the virtual server instance life cycle. It only removes the volume's association with the instance. You can delete data volumes manually after they are detached or, when creating a volume, you can specify that they be [automatically detached and deleted](/docs/vpc?topic=vpc-managing-block-storage#auto-delete) when the instance is deleted.

Data volumes are encryped by default with IBM-managed encryption.

## Encryption for data-at-rest
{: #encryption}

{{site.data.keyword.cloud_notm}} takes the need for security seriously and understands the importance of being able to encrypt data to keep it safe. When you create a standalone volume or create a volume as part of instance creation, you're data is protected with IBM-managed encryption. All boot and data volumes are encrypted with IBM-managed encryption. There is no additional cost for this service or impact on performance. Provider-managed encryption uses the following industry standard protocols:

* AES-256 encryption
* Keys are managed in-house with Key Management Interoperability Protocol (KMIP)
* Storage is validated for Federal Information Processing Standard (FIPS) Publication 140-2, Federal Information Security Management Act (FISMA), Health Insurance Portability and Accountability Act (HIPAA). Storage is also validated for Payment Card Industry (PCI), Basel II, California Security Breach Information Act (SB 1386), and EU Data Protection Directive 95/46/EC compliance.

## Related information

For more information about creating and managing instances in the VPC, see [About virtual server instances for VPC](/docs/vpc?topic=vpc-about-advanced-virtual-servers).

To get started with creating block storage for VPC, see [Creating block storage volumes](/docs/vpc?topic=vpc-creating-block-storage#creating-block-storage).

{{site.data.keyword.block_storage_is_short}} provides features unique to the VPC and is not compatible with the classic infrastructure storage. If you're interested in {{site.data.keyword.blockstoragefull}} on the classic infrastructure, see [{{site.data.keyword.blockstoragefull}}](/docs/BlockStorage?topic=BlockStorage-About).
{:note}
