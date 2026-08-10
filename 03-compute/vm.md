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
- A subscription can contain up to 25_000 VMs per region
- VM compute prices are shown as hourly rates, although usage is generally billed per second
- Multiple managed disks can be attached to an Azure VM

## Availability

- A single VM instance has a 99.9% availability Service Level Agreement (SLA) when all attached OS and data disks use Premium SSD or Ultra Disk storage
- Two or more VM instances deployed in an availability set provide a 99.95% availability SLA

## Networking Components

- Launching an Azure VM creates or associates the networking components required by the VM
- Network Security Group (NSG): a virtual firewall attached to a network interface or subnet that uses rules to control traffic by ports and protocols
- Network Interface (NIC): enables a VM to communicate with other resources by using network protocols
- Virtual machine instance: the running server
- Public IP address: an optional address used to access the VM from the internet
- Virtual Network (VNet): the private network in which the VM resides

## OS

- When we laucn an VM we need to chose an Image which has a specific Operating System
- Microsoft works closely with partner to ensue images available are updated and optimized for Azure runtime. Most of these images can be found in Azure Markterplace
- Examples of supported/partened OSes:
    - SUSE
    - Red Hat Enterprise Linux
    - Ubuntu Server
    - Debian
    - FreeBSD
    - Azure Marketplace - Flatcar Container Linux
    - RancherOS
    - Bitnami Library for Azure
    - Mesosphere DC/OS on Azure
    - Docker images
- We can bring our own Linux distribtuinb by creating a Virtual Hard Disk (VHD)
- Azure does not suppoert VHDX yet

## Cloud Init

- Cloud-init is the industry standard multi-distribution method for cross-platform cloud instance initialization
- It is supported across all major public cloud providers, provisioning systems for private cloud infrastructure, and bare-metal installations
- Cloud instances are initialized from a disk image and instance data with:
    - Meta-data
    - User-data: is a script that you want to run when an instance first boots up. eg. Install Apache web-server
    - Vendor-data
- Azure Virtual Machines supports cloud-init across most Linux Distros that support it

## Azure VMs - Sizes

- Azure VMs are available in a variety of sizes optimized for specific use cases
- Azure VM sizes are grouped by:
    - Type: workload category, such as general purpose or compute optimized
    - Size or series: hardware family, such as B or Dsv3, also called a Stock Keeping Unit (SKU) family
- **General purpose**: balanced CPU-to-memory ratio for testing and development, small to medium databases and low to medium traffic web servers
    - **SKUs**: B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4 and Ddsv4
- **Compute optimized**: high CPU-to-memory ratio for medium traffic web servers, network appliances, batch processes and application servers
    - **SKUs**: F, Fs and Fsv2
- **Memory optimized**: high memory-to-CPU ratio for relational database servers, medium to large caches and in-memory analytics
    - **SKUs**: Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2 and Dv2
- **Storage optimized**: high disk throughput and I/O for big data, SQL, NoSQL databases, data warehousing and large transactional databases
    - **SKUs**: Lsv2
- **GPU**: specialized VMs for heavy graphics rendering, video editing, model training and deep learning inference, available with single or multiple graphics processing units (GPUs)
    - **SKUs**: NC, NCv2, NCv3, NCasT4_v3 (preview), ND, NDv2 (preview), NV, NVv3 and NVv4
- **High-performance compute**: the fastest and most powerful CPU VMs, with optional high-throughput Remote Direct Memory Access (RDMA) network interfaces
    - **SKUs**: HB, HBv2, HC and H
- The selected VM image may limit the VM sizes available
- VM sizes can be sorted and filtered by options such as cost

## Azure Compute Unit

- Azure Compute Unit (ACU): provides a way to compare compute CPU performance across Azure Stock Keeping Units (SKUs)
- ACU is standardized on the Small (Standard_A1) VM with a value of 100
- Other SKUs indicate approximately how much faster they can run a standard benchmark
- SKU family comparison:
    - A1-A4: 100 ACU per vCPU with a 1:1 vCPU-to-core ratio
    - D1-D14: 160-250 ACU per vCPU with a 1:1 vCPU-to-core ratio
- D1-D14 VMs are approximately 60% to 150% more performant than A1-A4 VMs