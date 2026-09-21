# Azure Backup Service

- Azure Backup Service is a backup layer that spans many Azure services
- Azure Backup is directly integrated with Azure Services
- What can we backup?
    - On-Premise servers
    - Azure VMs
    - Azure Files
    - SQL Server (within Azure VM)
    - SAP HANNA databases (within Azure VM)
    - Azure Database for PostgreSQL server
- Why to use Azure Backup Service:
    - Offload on-premises backups
    - Backup your VMs
    - Scales Easily
    - Get unlimited data transfer (no limit and no charge)
    - Keep data secure (built-in security at-rest and in-transit)
    - Centralized monitoring and management
    - App Consistent Backups (restore apps back to an exact state)
    - Automatic Storage Management
    - Multiple Storage Options

## Azure Recovery Service (ARS) Vault

- It is a storage entity in Azure that houses data and recovery points
- The data itself is copies of data, or configuration information for VMs, workloads, servers, or workstations
- Can store backup data for various Azure services:
    - IaaS VMs (Linux or Windows)
    - Azure SQL databases
- Recovery Services vaults supports:
    - System Center (Data Protection Manager) DPM
    - Windows Server
    - Azure Backup Server
    - and more
- Recovery Services vaults has the following features:
    - Enhanced capabilities to help secure backup data
    - Central monitoring for your hybrid IT environment
    - Azure role-based access control (Azure RBAC)
    - Soft Delete
    - Cross Region Restore

## MARS Agent

- The Microsoft Azure Recovery Services (MARS) agent is used to back up files, folder and system state from Windows on-premises machines and Azure VMs to an Azure Recovery Services Vault
- Important:
    - Also known as Azure Backup agent
    - Does NOT support Linux
- Installation and configurations steps:
    - Create an Azure Recovery Service Vault
    - Define a backup policy in the vault
    - Optionally configure a secure connection using ExpressRoute/Private Endpoints
    - Download and install the MARS agent on Windows
    - Register the agent to enable backups

## Backup Policies

- To create a backup policy you choose a datasource type:
    - Azure VMs or PSQL database
    - Choose the frequency
- How many snapshots you want to retain
- Choose the time range for your retention

## Recovery Services Vault vs Backup Vault

- Recovery Services vault: protects Azure VMs, Azure Files, SQL Server and SAP HANA in Azure VMs, and supported on-premises workloads
- Backup vault: manages protection for newer supported data sources such as Azure managed disks, Azure Blobs and Azure Database for PostgreSQL Flexible Server
- The vault type is determined by the data source, not simply by the desired retention period
- Azure SQL Database has its own service-managed backups; it is different from backing up SQL Server inside an Azure VM
- For Azure VM backup, create the Recovery Services vault in the VM's region and subscription
- Select backup storage redundancy before protection begins; changing it later can be restricted
- Choose operational or vaulted protection where supported:
    - Operational protection uses recovery data close to the source, such as managed disk snapshots
    - Vaulted protection retains a separate managed backup copy for supported workloads
- A vault's managed identity needs the workload-specific permissions required to discover, back up and restore the data source
- A Recovery Services vault also manages Site Recovery configuration, but replication is separate from backup

## Configure Protection

- Select a data source, vault and backup policy, then enable protection
- A backup policy defines the schedule, time zone and retention for supported recovery point types
- Recovery point: stored state from which the protected data can be restored
- Retention can include daily, weekly, monthly and yearly recovery points where supported
- Azure VM Standard policy supports daily scheduled backup; Enhanced policy supports multiple scheduled backups per day
- Azure VM backup uses a VM extension rather than requiring the MARS agent inside every VM
- Application-consistent recovery points coordinate with supported applications; crash-consistent points reflect an abrupt shutdown state
- Instant Restore uses retained snapshots for faster initial VM recovery; vault retention is a separate setting
- Run an initial backup and confirm successful job completion before treating a workload as protected
- An on-demand backup supplements scheduled backups and uses a chosen retention period within supported limits

## Restore Operations

- Choose the protected item, recovery point and restore type
- Azure VM restore choices include creating a new VM, restoring disks or replacing existing disks where supported
- Restore disks when the VM configuration or networking must be rebuilt manually
- File recovery can mount a supported VM recovery point to recover selected files without restoring the entire VM
- Verify target-region capacity, network settings, identity permissions and encryption key access before restoring
- Cross Region Restore requires supported workloads and a suitably configured geo-redundant vault
- Cross Region Restore is different from using Site Recovery to fail over a running application
- Stop protection with retain data keeps existing recovery points and can continue to incur backup storage charges
- Stop protection with delete data removes backup data subject to soft delete and other protection controls

## Backup Security

- Soft delete retains supported deleted backup data for recovery during the configured retention period
- Enhanced soft delete supports configurable retention and an always-on setting that cannot be disabled once enabled
- Immutable vault settings prevent operations that would prematurely remove protected recovery points
- Locking vault immutability is irreversible, so validate policies before locking
- MUA (multi-user authorization) with Resource Guard requires separate authorization for protected critical operations
- Separate backup administration from approval permissions to reduce the impact of a compromised administrator
- A resource lock on the vault does not replace backup soft delete, immutability or permission controls

## Backup Reports and Alerts

- Use the centralized backup management experience or the vault to review protected items, jobs and alerts
- Configure vault diagnostic settings to send supported backup reporting data to a Log Analytics workspace
- Backup Reports use collected diagnostic data to analyze jobs, storage consumption and protection trends
- Reporting begins after diagnostic collection is configured; old data is not automatically backfilled
- Azure Monitor backup alerts identify events such as backup or restore failures
- Route notifications using action groups and applicable alert processing rules
- Investigate failed jobs, missed recovery points and unprotected workloads, not just successful recent backups