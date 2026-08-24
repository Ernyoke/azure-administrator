# Azure Managed Disks (also known as Azure Disks)

- Azure managed disks are virtualized block-level storage volumes managed by Azure and used with Azure virtual machines (VMs)
- We specify the disk type and size, and Azure manages the underlying storage
- Managed disks are designed for 99.999% availability
- Azure maintains three replicas of the data for high durability
- We can create up to 50,000 disks of each disk type in a subscription per region
- A virtual machine scale set can contain up to 1,000 VMs when using an Azure Marketplace or Azure Compute Gallery image with managed disks
- Managed disks integrate with availability sets by placing disks in separate storage scale units to reduce single points of failure
- Managed disks support Availability Zones for protection against datacenter failures
- Azure Backup supports scheduled backups and retention policies for managed disks
- We can directly upload virtual hard disk (VHD) and VHDX files up to 32 TiB to managed disks
- Azure Private Link restricts managed disk import and export traffic to the Microsoft backbone network

## Encryption

- Managed disk encryption options include:
    - Server-side encryption (SSE)
    - Encryption at host
    - Azure Disk Encryption (ADE)
    - Confidential disk encryption
- Server-side encryption (SSE):
    - Always enabled for OS disks, data disks, snapshots and images
    - Encrypts data at rest with 256-bit Advanced Encryption Standard (AES) encryption
    - Does not encrypt temporary disks or disk caches
    - Uses platform-managed keys by default
    - Supports customer-managed keys through a disk encryption set (DES) and Azure Key Vault or Azure Managed Hardware Security Module (HSM)
- Encryption at host:
    - Encrypts temporary disks and OS and data disk caches on the VM host
    - Keeps data encrypted while it flows between the compute host and Azure Storage
    - Does not use the VM's CPU
- Azure Disk Encryption (ADE):
    - Encrypts OS and data disks inside the guest VM
    - Uses BitLocker on Windows and DM-Crypt on Linux
    - Integrates with Azure Key Vault to manage encryption keys and secrets
    - Is scheduled for retirement on September 15, 2028
    - Encryption at host is recommended for new VMs
- Confidential disk encryption:
    - Protects the OS disk by binding encryption keys to the confidential VM's Trusted Platform Module (TPM)

## Azure Disk Roles

- There are three main disk roles in Azure:
    - Data disk:
        - A managed disk attached to a VM to store applications and persistent data
        - Registered as a Small Computer System Interface (SCSI) drive and assigned a drive letter or mount point in the guest OS
        - Can have a maximum capacity of 65,536 GiB when using Ultra Disk or Premium SSD v2
        - The VM size determines how many data disks we can attach and which disk types the VM supports
    - OS disk:
        - Every VM has one attached OS disk
        - Contains the preinstalled OS selected when the VM was created
        - This disk contains the boot volume
        - This disk has a maximum capacity of 4,095 GiB
        - A master boot record (MBR) partition supports only 2 TiB of usable space; GUID partition table (GPT) is required to use more
        - The OS disk is drive C on Windows VMs and is persistent unless the VM uses an ephemeral OS disk
    - Temporary disk:
        - Most VMs contain a temporary disk, which is not a managed disk
        - Provides short-term storage for page files, swap files and SQL Server `tempdb` files
        - Data may be lost during a maintenance event, VM redeployment or VM stop operation
        - Data persists during a successful standard VM restart
        - Typically mounted at `/dev/disk/azure/resource` on Linux and assigned drive D on Windows
        - Not encrypted by SSE because it is not a managed disk
        - Version 5 and later VM sizes encrypt temporary disks at rest automatically
        - Encryption at host also encrypts the temporary disk

## Managed Disk Snapshots and Managed Custom Images

- A full managed disk snapshot is a read-only, crash-consistent copy of one managed disk
- Snapshots provide point-in-time backups and exist independently of the source disk
- We can use a snapshot to create a new managed disk
- Incremental snapshots store only changes since the previous snapshot; the first incremental snapshot is a full copy
- Snapshots are billed based on used size, so a snapshot with 10 GiB of used data is billed for 10 GiB even if its source disk is provisioned for 64 GiB
- We can view snapshot usage in the Azure usage report
- A managed custom image is created from a generalized and deallocated VM
- The image contains all managed disks associated with the VM, including the OS and data disks
- A snapshot does not have awareness of any disks except the one it contains
- Snapshots do not coordinate data across multiple disks and are unsuitable for scenarios that require coordinated copies, such as disk striping
- Azure Compute Gallery is preferred over legacy managed images for distributing and versioning images at scale

## Disk Types

- Azure offers five managed disk types:
    - Ultra Disk:
        - Provides high throughput, high input/output operations per second (IOPS) and low latency
        - Performance can be changed dynamically without restarting the VM
        - Best for data-intensive workloads such as SAP HANA and top-tier databases
        - Can be used only as a data disk; the OS disk requires Premium SSD
        - Supported only by specific VM series
        - Does not support host caching, availability sets or zone-redundant storage (ZRS)
        - Maximum disk size: 65,536 GiB
        - Maximum throughput: 10,000 MB/s
        - Maximum IOPS: 400,000
    - Premium SSD v2:
        - High-performance, cost-effective SSD storage with improved scalability and flexibility
        - Capacity, IOPS and throughput can be configured independently
        - Best for SQL Server, Oracle, MariaDB, SAP, Cassandra, MongoDB, big data analytics and gaming
        - Cannot be used as an OS disk
        - Does not support host caching
        - Available only in select regions
        - Maximum disk size: 64 TiB
        - Maximum throughput: 2,000 MB/s
        - Maximum IOPS: 80,000
    - Premium SSD:
        - High-performance SSD storage for mission-critical applications
        - Provides guaranteed IOPS and throughput
        - Best for enterprise applications, databases and production VMs that require low latency
        - Provides low single-digit millisecond latency 99.9% of the time
        - Supported only by Premium Storage-compatible VM series
        - Maximum disk size: 32,767 GiB
        - Maximum throughput: 900 MB/s
        - Maximum IOPS: 20,000
    - Standard SSD:
        - Cost-effective storage for workloads that need consistent performance at lower IOPS
        - Provides better availability, consistency and reliability than Standard HDD
        - Best for web servers, low-IOPS enterprise applications and development or test environments
        - Provides single-digit millisecond latency 99% of the time
        - IOPS and throughput can vary based on traffic
        - Available for all Azure VMs
        - Maximum disk size: 32,767 GiB
        - Maximum throughput: 750 MB/s
        - Maximum IOPS: 6,000
    - Standard HDD:
        - Reliable, low-cost storage for latency-insensitive workloads
        - Best for backup, non-critical and infrequently accessed workloads
        - Has higher latency than SSD storage
        - Write latency is under 10 ms and read latency is under 20 ms
        - Available for all Azure VMs and in all Azure regions
        - Support for Standard HDD OS disks retires on September 8, 2028
        - Maximum disk size: 32,767 GiB
        - Maximum throughput: 500 MB/s
        - Maximum IOPS: 2,000 or 3,000 with performance plus enabled

## Disk Type Comparison

| Disk type | Best for | Maximum disk size | Maximum throughput | Maximum IOPS | OS disk support |
| --- | --- | ---: | ---: | ---: | --- |
| Ultra Disk | I/O-intensive workloads and top-tier databases | 65,536 GiB | 10,000 MB/s | 400,000 | No |
| Premium SSD v2 | Production and performance-sensitive workloads | 65,536 GiB | 2,000 MB/s | 80,000 | No |
| Premium SSD | Enterprise applications and production workloads | 32,767 GiB | 900 MB/s | 20,000 | Yes |
| Standard SSD | Web servers and development or test environments | 32,767 GiB | 750 MB/s | 6,000 | Yes |
| Standard HDD | Backup, non-critical and infrequently accessed workloads | 32,767 GiB | 500 MB/s | 2,000 or 3,000 with performance plus | Yes, retiring September 8, 2028 |

## Bursting

- Disk bursting temporarily increases storage input/output operations per second (IOPS) and throughput above the provisioned performance target
- Bursting helps handle short-lived demand such as VM startup, batch jobs and unexpected traffic spikes without permanently increasing the disk performance tier
- VM-level and disk-level bursting are independent:
    - A burst-capable disk does not require a burst-capable VM
    - A burst-capable VM does not require burst-capable disks
    - Effective performance is still limited by the applicable VM and disk IOPS and throughput limits
- Credit-based bursting:
    - Uses credits accumulated while IOPS or throughput remains below the provisioned target
    - Can burst for up to 30 minutes at the maximum burst rate when the credit bucket is full
    - Operates on a best-effort basis and does not incur extra charges
    - IOPS credits and throughput credits accumulate and are consumed independently
- VM-level bursting:
    - Uses only the credit-based model
    - Enabled by default for most Premium Storage-capable VM sizes
- Disk-level bursting supports two models:
    - Credit-based bursting:
        - Available for Premium SSD sizes P20 and smaller, up to 512 GiB
        - Available for Standard SSD sizes E30 and smaller, up to 1,024 GiB
        - Enabled by default for eligible new and existing disks
        - Available in all Azure Public, Government and China cloud regions
    - On-demand bursting:
        - Available only for Premium SSD managed disks larger than 512 GiB
        - Must be enabled manually and is not limited by a credit bucket or maximum burst duration
        - Incurs an hourly enablement fee and charges for burst transactions above the provisioned target
        - Can be enabled only while the disk is detached or the VM is stopped
        - Can be disabled 12 hours after it is enabled
- Ultra Disk and Premium SSD v2 do not use disk bursting because their provisioned IOPS and throughput can be adjusted directly
- Standard HDD does not support disk bursting

