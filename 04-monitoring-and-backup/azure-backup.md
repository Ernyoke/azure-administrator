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