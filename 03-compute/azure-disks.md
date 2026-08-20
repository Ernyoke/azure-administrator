# Azure Managed Disks (also known as Azure Disks)

- They are a virtual hard drive in the cloud Block-level storage volumes for SSD and HDD
- Azure Managed Disks are block-level storage volumes that are managed by Azure and used with Azure VMs
- Managed disks are designed for 99.999% availability
- Azure creates three replicas of your data, allowing for high durability
- We can create up to 50_000 VM disks of a type in a subscription per region
- We can create up to 1_000 VMs ina virtual machine scale set using a Marketplace image
- Managed disks are integrated with availability sets
- Managed disks support Availability Zones
- Azure Backup can be used to create a backup job with time-based backups and backup retention policies
- We can directly import our Virtual Hard drive Disks (VHD) into Azure Disks
- We can use Azure Private Links to ensure traffic between Azure Disks and VMs stay within the the Microsoft network

# Encryption

- Azure Managed Disks supports 2 types of encryption:
    - Server Side Encryption (SSE)
    - Azure Disk Encryption (ADE)
- Server Side Encryption (SSE):
    - Enabled by default for all managed disks, snapshots, and images
    - Temporary disk are not encrypted by server-side encryption unless we enable encryption at host
    - Keys can be managed two ways:
        - Platform-managed keys - Azure manages the keys
        - Customer-managed keys - We manage the keys
- Azure Disk Encryption (ADE):
    - Allows us to encrypt the OS and Data disks used by an IaaS Virtual Machine
    - For Windows encryption is done by BitLocker
    - For Linux encryption is done by DM-Crypt