# Azure Virtual Machines

## Overview

- Azure Virtual Machines (VMs) provide highly configurable servers that can be launched in minutes
- Choose an operating system (OS), compute, memory and storage configuration for the server
- Virtualization lets you run a server without buying and maintaining the physical hardware
- Azure VMs still require maintenance, including:
    - Applying OS patches
    - Installing and configuring software packages

## VM Configuration

- VM image: defines the OS and initial software installed on the VM
- VM size: defines the combination of virtual CPUs (vCPUs), memory and temporary storage capacity
- A subscription can contain up to 25000 VMs per region
- VM compute prices are shown as hourly rates, although usage is generally billed per second
- Multiple managed disks can be attached to an Azure VM

## Availability

- A single VM instance has a 99.9% availability Service Level Agreement (SLA) when all attached OS and data disks use Premium SSD or Ultra Disk storage
- Two or more VM instances deployed in an availability set provide a 99.95% availability SLA

## Availability Sets

- Availability set: logical grouping that distributes VMs across separate fault domains and update domains within a datacenter
- Fault domain: group of VMs that share a common power source and network switch
    - Azure supports up to three fault domains per availability set, depending on the region
    - VMs in different fault domains reduce the impact of hardware, power and network failures
- Update domain: logical group of VMs that can undergo planned maintenance and restart together
    - An availability set supports up to 20 update domains
    - Azure processes update domains one at a time during planned maintenance
- Azure automatically assigns each VM to a fault domain and an update domain
- Deploy at least two VMs in an availability set to qualify for the 99.95% SLA
- Availability sets provide redundancy within a datacenter, while availability zones provide redundancy across separate datacenters in a region
- A VM can be assigned to an availability set only when the VM is created
- Availability sets have no additional charge, but each VM and its associated resources are billed
- Use managed disks so disks attached to VMs are aligned with the VMs' fault domains

## Networking Components

- Launching an Azure VM creates or associates the networking components required by the VM
- Network Security Group (NSG): a virtual firewall attached to a network interface or subnet that uses rules to control traffic by ports and protocols
- Network Interface (NIC): enables a VM to communicate with other resources by using network protocols
- Virtual machine instance: the running server
- Public IP address: an optional address used to access the VM from the internet
- Virtual Network (VNet): the private network in which the VM resides

## OS

- A VM image defines the operating system and can include preinstalled software
- Azure Marketplace provides Microsoft and partner images optimized for Azure
- Common supported operating systems include:
    - Windows Server
    - Red Hat Enterprise Linux
    - SUSE Linux Enterprise Server
    - Ubuntu Server
    - Debian
    - Oracle Linux
- Custom Windows or Linux images can be created from a fixed-size Virtual Hard Disk (VHD)
- Azure VM disks do not support the VHDX format

## cloud-init

- cloud-init: widely used method for configuring Linux VMs during initial boot
- Can install packages, write files and configure users or security settings
- Uses instance data such as:
    - Metadata: information about the VM instance
    - User data: user-supplied configuration or scripts
    - Vendor data: configuration supplied by the image vendor or cloud platform
- Azure supports cloud-init on enabled Linux images and virtual machine scale sets
- The Azure Linux Agent is still required to process Azure VM extensions

## Custom Script Extension

- Custom Script Extension: downloads and runs scripts on Azure VMs for post-deployment configuration, software installation and management tasks
- Supports Windows and Linux VMs and depends on the Azure VM agent
- Scripts can be supplied at runtime or downloaded from an endpoint accessible to the VM, such as Azure Storage, GitHub or an internal file server
- Can be deployed through the Azure portal, Azure CLI, Azure PowerShell, an ARM template or the Azure Virtual Machines REST API
- Extension identifiers differ by operating system:
    - Windows publisher and type: `Microsoft.Compute` and `CustomScriptExtension`
    - Linux version 2 publisher and type: `Microsoft.Azure.Extensions` and `CustomScript`
- `fileUris`: specifies files for the extension to download
- `commandToExecute`: specifies the command or entry-point script to run
- Public settings are sent to the VM in clear text, while protected settings are encrypted and decrypted only inside the VM
- Secrets, storage keys and sensitive URLs must be placed in protected settings
- The VM requires network access to any endpoint from which the extension downloads files
- The extension runs a script only once and does not run it automatically at every VM startup
- Change the configuration, such as the `timestamp` value, or use a force update tag to trigger another run
- Only one version of an extension can be applied to a VM at a time
- Scripts should be idempotent, must not require user input and have a 90-minute execution limit
- Scripts should not restart the VM or stop or update the VM agent because the extension does not continue after a restart and can time out

## VM Sizes

- Azure VMs are available in a variety of sizes optimized for specific use cases
- Azure VM sizes are grouped by:
    - Type: workload category, such as general purpose or compute optimized
    - Series: group of sizes with similar hardware and features
    - Size: specific configuration of virtual CPUs (vCPUs), memory, storage and accelerators
- General purpose: balanced CPU-to-memory ratio for development, databases and web servers
- Compute optimized: high CPU-to-memory ratio for batch processing, network appliances and application servers
- Memory optimized: high memory-to-CPU ratio for relational databases, caches and in-memory analytics
- Storage optimized: high disk throughput and input/output (I/O) for databases, data warehousing and large transactional workloads
- Graphics processing unit (GPU) accelerated: specialized for compute-intensive, graphics-intensive and visualization workloads
- Field-programmable gate array (FPGA) accelerated: hardware acceleration for workloads such as machine learning inference and video transcoding
- High-performance computing (HPC): optimized for scientific and engineering workloads, often with Remote Direct Memory Access (RDMA)
- The selected VM image may limit the VM sizes available
- VM size availability varies by Azure region and subscription

## Azure Compute Unit

- Azure Compute Unit (ACU): provides a way to compare compute CPU performance across Azure Stock Keeping Units (SKUs)
- ACU is standardized on the Small (Standard_A1) VM with a value of 100
- ACU values provide an approximate relative comparison and do not guarantee performance
- Current Linux CoreMark and Windows SPECInt benchmark scores provide more detailed compute comparisons

## Hyper-V and Generation 1 vs Generation 2

- Hyper-V: Microsoft's hardware virtualization platform for creating and running VMs
- There are two generations of Hyper-V VMs:
    - Generation 1: BIOS boot with IDE disk controllers
    - Generation 2: Unified Extensible Firmware Interface (UEFI) boot and SCSI disk controllers
- Generation 2 capabilities include:
    - Potentially faster boot and installation times
    - OS disks larger than 2 TiB, up to a supported maximum of 4 TiB
    - Secure Boot and virtual Trusted Platform Module (vTPM) when Trusted Launch is enabled
- Not every VM size or image supports Generation 2
- Azure supports fixed-size VHD files but not VHDX files for both VM generations

## SSH, RDP and Bastion

- Secure Shell (SSH): protocol used to establish a secure terminal connection between a client and server
    - Commonly used to remotely connect to Linux VMs
    - Uses TCP port 22
    - SSH key pairs or passwords can be used to authenticate access
- Remote Desktop Protocol (RDP): Microsoft protocol that provides a graphical interface for connecting to a remote computer
    - Commonly used to remotely connect to Windows Server VMs
    - Uses TCP and UDP port 3389
- Azure Bastion: managed service that provides RDP and SSH connectivity to VMs through the Azure portal and a web browser
    - Connections from the Azure portal use Transport Layer Security (TLS)
    - VMs do not require public IP addresses for Bastion connections
    - Acts as a hardened jump host between users and target VMs

## Azure Bastion

### Key Benefits

- Provides browser-based RDP and SSH access without installing additional clients or agents
- Supports devices that cannot run traditional RDP clients, such as Chromebooks
- Avoids exposing target VMs directly to the public internet

### Subnet Configuration

- Azure Bastion SKUs other than Developer require a dedicated subnet in the virtual network (VNet):
    - Subnet name: must be `AzureBastionSubnet`
    - Minimum subnet size: `/26`
    - The subnet cannot contain other resources
- The VNet address space must have sufficient capacity for the Bastion subnet
- Bastion Developer uses shared infrastructure and does not require `AzureBastionSubnet`

### Connecting to Windows VMs

- Azure Bastion provides RDP access to Windows VMs through the Azure portal
- Authenticate by using the username and password configured for the VM
- No additional client software is required

### Connecting to Linux VMs

- Azure Bastion provides SSH access to Linux VMs through the Azure portal
- Supported authentication methods include:
    - Username and password
    - SSH private key
- The SSH private key file can be provided when starting the connection

### Exam Notes

- Target VMs do not require public IP addresses
- Most Bastion deployments require `AzureBastionSubnet`, but Bastion Developer does not
- Most Bastion deployments require a Standard static public IP address, while Developer and private-only deployments do not

## Azure Update Manager

- Azure Update Manager: unified service for assessing update compliance and installing operating system updates on Windows and Linux machines
- Supported machines include:
    - Azure VMs
    - On-premises and multicloud machines connected through Azure Arc
- Update options include:
    - Check for updates on demand
    - Install one-time updates
    - Schedule updates within a maintenance window
    - Enable periodic assessment to check for updates every 24 hours
    - Use automatic VM guest patching for Azure VMs
- Update Manager honors each machine's configured update source, such as Windows Update, Microsoft Update, Windows Server Update Services (WSUS) or a Linux package repository
- Update Manager has no dependency on Azure Automation or a Log Analytics workspace
- Required update extensions are installed and managed automatically when an update operation first runs
- Azure Automation Update Management and its Microsoft Monitoring Agent (MMA) dependency are retired legacy technologies

