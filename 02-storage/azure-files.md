# Azure Files

- Azure Files provides fully managed file shares in the cloud accessible over standard file system protocols
- Shares are hosted inside a storage account and can be mounted by Windows, Linux and macOS clients
- Supported protocols:
    - SMB (Server Message Block): versions 2.1, 3.0 and 3.1.1
    - NFS (Network File System): version 4.1, premium file shares only
- Can be mounted concurrently by cloud and on-premises clients
- Common use cases:
    - Replace or supplement on-premises file servers
    - Lift-and-shift applications that expect a file share
    - Shared application settings, diagnostic shares and development tools
- Encrypted at rest with Storage Service Encryption (SSE) and in transit with SMB 3.x encryption
- SMB 3.0 or later is required to mount a share outside an Azure region because port 445 must be open outbound

## Share Tiers

- Premium:
    - Uses FileStorage account kind with SSD backed storage
    - Provisioned billing model, size determines IOPS and throughput
    - Only tier that supports NFS 4.1
- Transaction optimized:
    - Standard HDD tier for transaction heavy workloads
    - Highest storage price, lowest transaction price
- Hot:
    - Standard HDD tier for general purpose file sharing such as team shares
- Cool:
    - Standard HDD tier for archive-like or infrequently accessed data
    - Lowest storage price, highest transaction price
- Standard tiers are hosted in a general purpose v2 (GPv2) account and billed as pay-as-you-go
- Tier can be changed between standard tiers, but not between standard and premium

## Share Limits and Redundancy

- Standard shares support up to 5 TiB by default and up to 100 TiB with large file shares enabled
- Large file shares require LRS or ZRS and cannot be enabled on GRS or GZRS accounts
- Premium shares support up to 100 TiB without extra configuration
- Redundancy options:
    - Standard: LRS, ZRS, GRS, GZRS
    - Premium: LRS and ZRS only
- Read-access replicas (RA-GRS, RA-GZRS) are not usable for file shares because SMB cannot read from the secondary endpoint

## Identity and Authorization

- Authentication options:
    - Storage account key: full administrative access to the share
    - Identity-based over SMB using Kerberos:
        - On-premises AD DS
        - Microsoft Entra Domain Services
        - Microsoft Entra Kerberos for hybrid identities
- Share-level permissions are granted with Azure RBAC roles:
    - Storage File Data SMB Share Reader: read access
    - Storage File Data SMB Share Contributor: read, write and delete
    - Storage File Data SMB Share Elevated Contributor: read, write, delete and modify NTFS permissions
- Directory and file level permissions use standard Windows NTFS ACLs (access control lists)
- Shared Access Signature (SAS) tokens are only supported for the REST API, not for SMB mounts

## Network Access

- Public endpoint access can be restricted with storage account firewall rules and virtual network service endpoints
- Private endpoints give the share a private IP inside a virtual network and avoid opening port 445 to the internet
- Access from on-premises requires port 445 outbound, a site-to-site VPN, point-to-site VPN or ExpressRoute
- Secure transfer required forces SMB 3.x with encryption and HTTPS for REST

## Snapshots and Backup

- Share snapshots are read-only point-in-time copies of an entire file share
- Snapshots are incremental, only changed blocks are stored after the first snapshot
- Up to 200 snapshots can be retained per share
- Deleting a share deletes all its snapshots, so snapshots must be deleted before the share is removed
- Soft delete for file shares allows recovery of a deleted share within a retention period of 1 to 365 days
- Azure Backup protects file shares using snapshot-based backup and does not require an agent

## Azure File Sync

- Azure File Sync caches Azure file shares on one or more on-premises or Azure Windows Servers
- Turns Windows Server into a fast cache of the Azure file share while the share stays the authoritative copy
- Requires Windows Server 2012 R2 or later with NTFS volumes
- Benefits:
    - Centralizes file shares in Azure while keeping local access performance
    - Multi-site sync keeps several servers in sync with the same share
    - Cloud tiering reduces on-premises storage consumption
    - Enables backup and disaster recovery of on-premises data through Azure

## Azure File Sync Components

- Storage Sync Service: top-level Azure resource that is the parent of all sync relationships and is separate from the storage account
- Sync group: defines the sync topology for a set of files, all endpoints within a sync group stay in sync with each other
- Cloud endpoint: an Azure file share that is part of a sync group, a share can only belong to one cloud endpoint at a time
- Server endpoint: a specific path on a registered server, such as a volume or folder, that is part of a sync group
- Registered server: a trust relationship between a Windows Server and the Storage Sync Service
- Azure File Sync agent: downloadable package installed on Windows Server that enables sync, made up of three components:
    - FileSyncSvc.exe: background service that watches for changes and starts sync sessions
    - StorageSync.sys: file system filter driver that supports cloud tiering
    - PowerShell management cmdlets for the agent
- A sync group can contain only one cloud endpoint but many server endpoints
- Servers in a sync group must be registered to the same Storage Sync Service

## Azure File Sync Deployment Steps

- Deploy a Storage Sync Service in the same region as the storage account
- Prepare Windows Server, disable Internet Explorer Enhanced Security Configuration during agent setup
- Install the Azure File Sync agent and register the server with the Storage Sync Service
- Create a sync group and add the Azure file share as the cloud endpoint
- Add a server endpoint by selecting the registered server and a local path
- Optionally enable cloud tiering on the server endpoint

## Cloud Tiering

- Cloud tiering is an optional feature of a server endpoint that keeps frequently accessed files local and tiers the rest to Azure
- Tiered files appear locally as pointers, or reparse points, and are recalled transparently on access
- Policies that control tiering:
    - Volume free space policy: minimum percentage of free space to keep on the volume, evaluated first
    - Date policy: tiers files not accessed within a specified number of days
- Only the file content is tiered, the namespace and file metadata always stay on the server
- Cloud tiering is not supported on the Windows system volume
