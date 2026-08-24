# Storage Accounts

- Azure Storage offers several types of storage accounts, each with different features and pricing models
- Storage account types:
    - General purpose v1 (legacy)
    - General purpose v2
    - BlobStorage (legacy)
    - BlockBlobStorage
    - FileStorage
- Storage account type is shown as Account kind in the Azure portal

## Storage Account Comparison

| Account type | Supported services | Performance tiers | Access tiers | Replication | Deployment models |
| --- | --- | --- | --- | --- | --- |
| Standard general purpose v2 | Blob, Data Lake Storage, Files, Queue, Table | Standard | Hot, cool, cold, archive | LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS | Resource Manager |
| Premium block blobs | Block blobs, append blobs, Data Lake Storage | Premium | Not applicable | LRS, ZRS | Resource Manager |
| Premium file shares | Files | Premium | Not applicable | LRS, ZRS | Resource Manager |
| Premium page blobs | Page blobs | Premium | Not applicable | LRS, ZRS | Resource Manager |
| General purpose v1 (legacy) | Blob, Files, Queue, Table | Standard, Premium | Not applicable | LRS, GRS, RA-GRS | Resource Manager, classic (retired) |
| BlobStorage (legacy) | Block blobs, append blobs | Standard | Hot, cool | LRS, GRS, RA-GRS | Resource Manager |

## Core Storage Services

- Azure has 5 core storage services:
    - Azure Blob Storage: massively scalable object storage for text and binary data, including big data analytics through Data Lake Storage Gen2
    - Azure Files: managed file shares for cloud and on-premises deployments
    - Azure Queue Storage: messaging storage for reliable communication between application components
    - Azure Table Storage: NoSQL storage for schemaless structured data
    - Azure Disk Storage: block-level storage volumes for Azure virtual machines

## Performance Tiers (Blob Storage)

### Key Performance Tiers

- Standard performance:
    - Stored on Hard Disk Drives (HDDs)
    - Use cases: backups, media content and bulk processing
    - Performance and cost depend on the access tier: hot, cool, cold or archive
    - HDDs have moving parts and are slower than Solid State Drives (SSDs)
- Premium performance:
    - Stored on SSDs
    - Use cases: interactive workloads, AI and Machine Learning (ML), and analytics
    - Optimized for low latency and high throughput
    - SSDs have no moving parts, enabling faster data operations

### What Is IOPS?

- IOPS (Input/Output Operations Per Second): measures the number of read and write operations a storage device can perform each second
- Higher IOPS generally means faster read and write performance

### Important Considerations for IOPS

- IOPS varies with disk size
- Premium SSD managed disk examples:
    - P1 disk:
        - Size: 4 GiB
        - Base performance: 120 IOPS and 25 MB/s
        - Maximum burst performance: 3,500 IOPS and 170 MB/s
    - P80 disk:
        - Size: 32,767 GiB
        - Base performance: 20,000 IOPS and 900 MB/s
        - Maximum burst performance with on-demand bursting enabled: 30,000 IOPS and 1,000 MB/s

## Access Tiers (Blob Storage)

- Blob Storage has 4 fixed access tiers: hot, cool, cold and archive
- Smart tier automatically moves data between the hot, cool and cold tiers based on usage patterns
- Access tiers apply to block blobs, not append blobs or page blobs
- Hot, cool and cold are online tiers with immediate access
- Archive is an offline tier that requires rehydration before data can be accessed

### Hot

- Data that is accessed or modified frequently
- Highest storage cost and lowest access cost
- Use cases:
    - Data in active use or expected to be accessed frequently
    - Data staged for processing and eventual migration to the cool tier

### Cool

- Data that is infrequently accessed and stored for at least 30 days
- Lower storage cost and higher access cost than the hot tier
- Use cases:
    - Short-term backup and disaster recovery datasets
    - Older media content that is viewed infrequently but must remain immediately available
    - Large datasets stored cost-effectively while more data is gathered for future processing

### Cold

- Data that is rarely accessed and stored for at least 90 days
- Lower storage cost and higher access cost than the cool tier
- Use cases:
    - Legal or financial records that are rarely accessed but must be retained
    - Seasonal data that must be preserved when out of season

### Archive

- Data that is rarely accessed and stored for at least 180 days
- Lowest storage cost and highest access cost
- Supported only with LRS, GRS and RA-GRS redundancy
- Not supported with ZRS, GZRS or RA-GZRS redundancy
- Use cases:
    - Long-term backup, secondary backup and archival datasets
    - Original raw data that must be preserved after processing
    - Compliance and archival data retained for long periods and rarely accessed

### Account-Level Tiering

- A blob without an explicitly assigned tier inherits the storage account's default access tier
- The default account access tier can be hot, cool or cold
- Archive cannot be the default account access tier

### Blob-Level Tiering

- A block blob can be uploaded directly to a selected access tier
- Moving between online tiers or from an online tier to archive is immediate
- Moving from archive to an online tier requires rehydration

### Rehydrating a Blob

- Rehydration: moving an archived blob to the hot, cool or cold online tier
- Rehydration can take up to 15 hours depending on the selected priority
- Archived blob data cannot be read or modified until rehydration completes

### Blob Lifecycle Management

- Rule-based policies can transition blobs between tiers or delete them when conditions are met
- Example: move a blob to the cool tier after 30 days
- Lifecycle management cannot rehydrate an archived blob to an online tier

### Tier Change Billing

- A blob is charged at the destination tier's rate immediately after upload or tier change
- Moving to a cooler tier:
    - Billed as a write operation to the destination tier
    - Destination-tier write operation charges apply
    - Per-GB data write charges also apply to legacy BlobStorage accounts
- Moving to a warmer tier:
    - Billed as a read operation from the source tier
    - Source-tier read operation and per-GB data retrieval charges apply
    - Early deletion charges can apply when leaving cool, cold or archive early
- Minimum retention periods for GPv2 accounts:
    - Cool: 30 days
    - Cold: 90 days
    - Archive: 180 days
- Early deletion charges are prorated for the remaining minimum retention period

## Replication and Data Redundancy

- A replication type must be selected when creating a storage account
- Replication stores multiple copies of data to protect against:
    - Planned events
    - Transient hardware failures
    - Network or power outages
    - Large-scale natural disasters
- Higher redundancy levels have higher replication costs
- Primary-region redundancy:
    - Locally redundant storage (LRS)
    - Zone-redundant storage (ZRS)
- Secondary-region redundancy for disaster recovery and failover:
    - Geo-redundant storage (GRS)
    - Geo-zone-redundant storage (GZRS)
- Secondary-region redundancy with read access to replicas:
    - Read-access geo-redundant storage (RA-GRS)
    - Read-access geo-zone-redundant storage (RA-GZRS)

### Primary-Region Redundancy

- Azure Storage always maintains multiple copies of data within the primary region
- Primary-region replication is synchronous
- Primary-region redundancy options:
    - Locally redundant storage (LRS)
    - Zone-redundant storage (ZRS)

### Locally Redundant Storage

- Replicates data within a single physical datacenter in the primary region
- Replication is synchronous
- Durability: at least 99.999999999% (11 nines) over a given year
- Lowest-cost redundancy option
- Protects against:
    - Drive failure
    - Server failure
    - Rack failure
- Does not protect against:
    - Datacenter failure
    - Zone-wide failure

### Zone-Redundant Storage

- Replicates data across three or more availability zones in the primary region
- Replication is synchronous
- Durability: at least 99.9999999999% (12 nines) over a given year
- Protects against:
    - Datacenter failure
    - Availability zone failure
- Costs more than LRS

### LRS and ZRS Comparison

- LRS:
    - Single datacenter
    - No zone-level resilience
    - Lowest-cost option
- ZRS:
    - Three or more availability zones
    - Higher availability

### Exam Trap

- LRS is not zone-resilient
- ZRS, GZRS and RA-GZRS provide availability zone protection within the primary region

### Secondary-Region Redundancy

- Replicates data to a secondary region to protect against a primary-region disaster
- The secondary region is determined by the primary region's Azure region pair and cannot be changed
- With GRS and GZRS, the secondary region is not available for read or write access unless failover occurs
- RA-GRS and RA-GZRS provide read access to the secondary region without requiring failover

### Geo-Redundant Storage

- Replicates data synchronously within the primary region by using LRS
- Replicates data asynchronously from the primary region to the secondary region
- Replicates data synchronously within the secondary region by using LRS
- Durability: at least 99.99999999999999% (16 nines) over a given year

### Geo-Zone-Redundant Storage

- Replicates data synchronously across three or more availability zones in the primary region by using ZRS
- Replicates data asynchronously from the primary region to the secondary region
- Replicates data synchronously within the secondary region by using LRS
- Durability: at least 99.99999999999999% (16 nines) over a given year

### Secondary-Region Redundancy with Read Access

- RA-GRS and RA-GZRS provide read-only access to data in the secondary region without requiring failover
- Azure Files does not support RA-GRS or RA-GZRS
- Replication within the primary region is synchronous
- Replication from the primary region to the secondary region is asynchronous
- The secondary copy can lag behind the primary copy

### Read-Access Geo-Redundant Storage

- Replicates data synchronously within the primary region by using LRS
- Replicates data asynchronously from the primary region to the secondary region
- Replicates data synchronously within the secondary region by using LRS
- Provides read access to the secondary region
- Durability: at least 99.99999999999999% (16 nines) over a given year

### Read-Access Geo-Zone-Redundant Storage

- Replicates data synchronously across three or more availability zones in the primary region by using ZRS
- Replicates data asynchronously from the primary region to the secondary region
- Replicates data synchronously within the secondary region by using LRS
- Provides read access to the secondary region
- Durability: at least 99.99999999999999% (16 nines) over a given year

