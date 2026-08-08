# Storage Accounts

- Azure Storage offers several types of storage accounts, each with different features and their pricing models
- Storage account types:
    - General purpose v1 (legacy)
    - General purpose v2
    - BlobStorage (legacy)
    - BlockBlogStorage
    - FileStorage
- Storage type and Account Kind means the same thing in the Azure portal

## Storage Account Comparison

| Account type | Supported services | Performance tiers | Access tiers | Replication | Deployment models |
| --- | --- | --- | --- | --- | --- |
| General purpose v2 | Blob, Data Lake Storage, Files, Queue, Table | Standard, Premium | Hot, cool, cold, archive | LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS | Resource Manager |
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
        - Size: 32 TiB
        - Base performance: 20,000 IOPS and 900 MB/s
        - Maximum burst performance: 30,000 IOPS and 1,000 MB/s

## Access Tiers (Blob Storage)

- Standard Blob Storage has 4 access tiers: hot, cool, cold and archive
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

