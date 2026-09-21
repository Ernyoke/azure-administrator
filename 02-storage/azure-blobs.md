# Azure Blob Storage

- Blob storage is a object-store that is optimized for storing massive amounts of unstructured data
- Unstructured data is data that doesn't adhere to a particular data model or definition, such as text or binary data
- Azure Blobs are composed of the components:
    - Storage Account: a unique namespace in Azure for your data http://mystorageaccount.blob.core.windows.net
    - Containers: similar to a folder in a file system
    - Blobs: the actual data being stored

## Containers and Blob Types

- Create a container in a storage account before uploading blobs
- Container names must be 3 to 63 characters long and use lowercase letters, numbers and single hyphens between alphanumeric characters
- Block blob: general-purpose object storage for documents, images and other files
- Append blob: optimized for append operations, such as logging
- Page blob: optimized for random reads and writes, including virtual hard disks
- Anonymous access levels:
    - Private: no anonymous access
    - Blob: anonymous read access to blobs, but not container listing
    - Container: anonymous read access to blobs and container listing
- Anonymous access must be permitted at the storage account level before a container can allow it
- Authenticated data access still requires a data role, account key or valid shared access signature (SAS)

## Soft Delete and Versioning

- Blob soft delete: retains deleted blobs and supported overwritten data for a configured period of 1 to 365 days
- Container soft delete: allows recovery of deleted containers during a retention period of 1 to 365 days
- Blob soft delete alone does not recover a deleted container
- Container soft delete does not recover individual blobs deleted inside a container that still exists
- Versioning automatically retains previous blob versions when supported write operations modify a blob
- Restore an older version by copying it to become the current blob
- With versioning enabled, deleting the current blob leaves previous versions available until explicitly removed or expired
- Snapshots are manually created read-only point-in-time copies of a blob
- Versions and snapshots incur storage charges; use lifecycle rules to manage retained copies
- Blob versioning is not supported on accounts with a hierarchical namespace enabled
- Neither blob nor container soft delete restores a deleted storage account; use a resource lock to help protect the account

## Lifecycle Rules

- Define rules using filters such as blob type, name prefix or blob index tags
- Apply actions to current versions, previous versions and snapshots as supported
- Base actions on elapsed time since modification, creation or last access, depending on the action and object type
- Enable last access time tracking before using access-time-based rules
- Example policy:
    - Move current block blobs to cool after 30 days without modification
    - Move them to archive after 180 days when the account redundancy supports archive
    - Delete previous versions after a separately configured retention period
- Lifecycle execution is asynchronous, not an immediate action at the threshold
- Deleting or moving data out of a tier before its minimum retention period can incur early deletion charges
- Lifecycle policies cannot rehydrate archived blobs; initiate rehydration separately

## Object Replication

- Object replication asynchronously copies block blobs between source and destination storage accounts
- Unlike account geo-redundancy, replication rules select containers and optional blob prefixes
- Supports general-purpose v2 and premium block blob accounts
- Enable blob versioning on both accounts and change feed on the source account
- Create source and destination containers and configure matching replication policies
- Rules can select all existing blobs, new blobs or blobs created after a chosen time
- Destination blobs cannot be modified directly while governed by the replication rule
- Does not support append blobs, page blobs or accounts with a hierarchical namespace enabled
- Archived blobs are not replicated; verify access tiers before selecting data for replication
- Cross-tenant replication depends on the storage account configuration and is disabled by default for newly created accounts