# Replace the system disk \(non-public image\) {#concept_vbb_ckj_ydb .concept}

Replacing the system disk refers to allocating a new system disk with a new ID to you and releasing the original one. You can replace the system disk if you choose an incorrect OS when creating an ECS instance or you need to replace the OS as your services grow.

You can replace the image of the system disk with a public image, shared image, customized image, or any other image from the marketplace.

**Note:** Microsoft has terminated technical support for Windows Server 2003. For the purpose of data security, it is not recommended that you continue running Windows Server 2003 on your ECS instance. Its image is no longer provided. For more information, see [Offline announcement of Windows Server 2003 system image](https://partners-intl.aliyun.com/help/faq-detail/59513.htm).

After you replace the system disk,

-   a new system disk with a new disk ID is allocated to your instance, and the original one is released.

-   The cloud type of the cloud disk cannot be replaced.

-   The IP address and the MAC address remain unchanged.

-   You can [delete snapshots or automatic snapshot policies](reseller.en-US/User Guide/Snapshots/Delete snapshots or automatic snapshot policies.md#) to ensure sufficient snapshot quota for executing automatic snapshot policies of the new system disk.


This document describes how to replace an existing image with a non-public image. If you need to use a public image, refer to [Replace the system disk \(public image\)](reseller.en-US/User Guide/Cloud disks/Replace the system disk (public image).md#).

## Precautions {#section_zdm_dkj_ydb .section}

Replacing the system disk is highly risky. Read the following precautions carefully before you begin:

**Risks**

The risks of replacing the system disk are as follows:

-   Replacing the system disk will stop your instances, and therefore interrupt your services.

-   After replacing the system disk, you must redeploy the service running environment on the new system disk. This may result in a long service interruption.

-   After you replace the system disk, a new system disk with a new disk ID will be assigned to your instance. It means that you cannot use snapshots of the original system disk to roll back the new system disk.

    **Note:** After you replace the system disk, the snapshots you have manually created are not affected. You can still use them to create customized images. If you have configured automatic snapshot policies for the original system disk to allow automatic snapshots to be released along with the disk, the snapshot policies do not work any more and all automatic snapshots of the original system disk will be automatically deleted.


**Precautions for cross-OS disk replacement**

Cross-OS disk replacement means replacing the system disk between Linux and Windows.

**Note:** Regions outside mainland China do not support disk replacement between Linux and Windows. Disk replacement between Linux editions or Windows editions are supported.

During cross-OS disk replacement, the file format of the data disk may be unidentifiable.

-   If no important data exists on the data disk, it is recommended that you [reinitialize the disk](reseller.en-US/User Guide/Cloud disks/Reinitialize a cloud disk.md#) and format it to the default file system of your OS.

-   If important data exists in your data disk, do the following:

    -   From Windows to Linux: Install a software application, for example, NTFS-3G, because NTFS cannot be identified by Linux by default.
    -   From Linux to Windows: Install a software application, for example, Ext2Read or Ext2Fsd, because ext3, ext4, and XFS cannot be recognized by Windows by default.

If you replace Windows with Linux, you can use a password or an SSH key pair for authentication.

## Prerequisites {#section_f2m_dkj_ydb .section}

Before replacing the existing image with a non-public image, do the following:

-   If the target image is a customized image:

    -   If you want to use an image of a specified ECS instance, you must [create a snapshot for the system disk of the specified instance](reseller.en-US/User Guide/Snapshots/Create snapshots.md#) and [create a customized image using a snapshot](reseller.en-US/User Guide/Images/Create custom image/Create a custom image by using a snapshot.md#). If the specified instance and the one whose system disk you want to change are located in different regions, you need to [copy images](reseller.en-US/User Guide/Images/Copy images.md#).
    -   To use an local physical image file, [import it on the ECS console](reseller.en-US/User Guide/Images/Import images/Import custom images.md#) or [use Packer to create and import the local image](reseller.en-US/User Guide/Images/Open source tools/Create and import on-premise images by using Packer.md#). The region where the image is located must be the same as your instance.
    -   To use an image in a region other than that of your instance, [copy the image](reseller.en-US/User Guide/Images/Copy images.md#) first.

        **Note:** The imported or duplicated images will be displayed in the **Custom Image** drop-down list.

-   To use an image owned by other Alibaba Cloud account, [share the image](reseller.en-US/User Guide/Images/Share images.md#) first.

-   If you want to replace the OS to Linux and use an SSH key pair for authentication, [create an SSH key pair](reseller.en-US/User Guide/Key pairs/Create an SSH key pair.md#) first.

-   Replacing the system disk is highly risky and may cause data loss or service interruption. To minimize the impact, it is recommended that you [create snapshots](reseller.en-US/User Guide/Snapshots/Create snapshots.md#) for the original system disk before replacement.

-   If you want to replace the OS to Linux, make sure that there is sufficient system disk space. It is recommended that you reserve 1 GiB in case the OS cannot properly start after system disk replacement.


**Note:** It is recommended that you create snapshots during off-peak hours. It may take about 40 minutes to create a snapshot of 40 GiB for the first time. Therefore, you must leave sufficient time for snapshot creation. Additionally, creating a snapshot may reduce I/O performance of a block storage device by 10% at most, resulting in sharp decrease in I/O rate.

## Procedure {#section_k2m_dkj_ydb .section}

1.  Log on to the [ECS Console](https://partners-intl.console.aliyun.com/#/ecs).
2.  In the left navigation pane, click **Instances**.
3.  Select a region.
4.  In the **Actions** column of the target instance, choose **More** \> **Instance Status** \> **Stop** and follow the message to stop the instance.

    When the instance status becomes **Stopped**, the instance has been stopped.

5.  In the **Actions** column, choose **More** \> **Disk and Image** \> **Replace System Disk**.
6.  In the displayed dialog box, read the precautions for system disk replacement, click **OK**.
7.  On the Replace System Disk page,
    1.  **Image Type**: Select **Custom Image**, **Shared Image**, or **Marketplace Image**, and then choose the image version.
    2.  **System Disk**: Unchangeable. However, you can expand the disk space to meet the requirements of your system disk and services. The maximum disk space is 500 GiB. The minimum space of the system disk you can configured is determined by the current disk space and image type.

        |Image|Maximum space \(GiB\)|
        |:----|:--------------------|
        |Linux \(excluding CoreOS\) + FreeBSD|\[Max\{20, current disk space\}, 500\]|
        |CoreOS|\[Max\{30, current disk space\}, 500\]|
        |Windows|\[Max\{40, current disk space\}, 500\]|

        **Note:** If your instance has been configured with [renewal for configuration downgrade](reseller.en-US/User Guide/Instances/Change configurations/Overview of configuration changes.md#), you cannot change the system disk space until the next billing cycle.

    3.  **Security enhancement**:
        -   If the new OS Windows, you can only use a password for authentication.
        -   If the instance is an I/O optimized instance, and the new OS is Linux, you can use either a password or an SSH key pair for authentication. In this case, set a login password or bind an SSH key pair.
    4.  Confirm **Instance Cost** : includes the image fee and system disk fee.
    5.  Check the configuration and click **Confirm to change**.

Log on to the ECS console to monitor the system status. It may take about 10 minutes to replace the OS. After the OS is replaced, the instance automatically starts.

## What to do next {#section_v2m_dkj_ydb .section}

After replacing the system disk, you may have to perform the following operations:

-   \(Optional\) [Apply automatic snapshot policies to disks](reseller.en-US/User Guide/Snapshots/Apply automatic snapshot policies to disks.md#). Automatic snapshot policies are bound to the disk ID. After the system disk is replaced, automatic snapshot policies applied on the original disk automatically fail. You need to configure automatic snapshot policies for the new system disk.

-   All mounting information will be lost if the OS is a Linux before and after disk replacement, and if a data disk is mounted to the instance and the partition is set to be mounted automatically at instance startup. You need to write the new partition information into the /etc/fstab file of the new system disk and mount the partition, but do not need to partition or format the data disk for another time. The steps are described as follows. For more information about operation commands, see [Format and mount data disks for Linux instances](../../../../reseller.en-US/Quick Start for Entry-Level Users/Step 4. Format a data disk/Format and mount data disks for Linux instances.md#).

    1.  \(Recommended\) Back up the /etc/fstab file.
    2.  Write information about the new partition into the /etc/fstab file.
    3.  Check the information in the /etc/fstab file.
    4.  Run `mount` to mount the partition.
    5.  Run `df-h -h` to check the file system space and usage.

        After the data partition is mounted, the data disk is ready for use without instance restart.


## Related API {#section_y2m_dkj_ydb .section}

[ReplaceSystemDisk](../../../../reseller.en-US/API Reference/Disk/ReplaceSystemDisk.md#)

