# Scale Sets

- Azure Scale Sets allow us to automatically increase or decrease VM capacity
- We creatre scaling policies to automatically add or remove instances based on Host Metrics
- We can create Health checks and set Repair Policies to replace unhealthy instances
- We can associate a Load Balancer to distribute VMs across AZs
- We can salce to 100s/1000s of VMs using scale sets
- A scale set itself is free, you only pay for the underlying VMs, disks and networking

## Orchestration Modes

- The orchestration mode is chosen at creation time and cannot be changed afterwards
- Flexible (recommended):
    - Manages standard Azure IaaS VMs (`Microsoft.Compute/virtualMachines`)
    - Instances can be different VM sizes and operating systems
    - Can mix Spot and Regular priority instances
    - Supports Azure Backup, Azure Site Recovery, resource tags and per-VM RBAC
    - Existing VMs can be added to the scale set
    - Up to 1000 instances
    - Does not support automatic OS image upgrades, overprovisioning, Basic Load Balancer or system-assigned managed identity
    - Health monitoring must use the Application Health Extension
- Uniform:
    - Manages scale set specific VMs (`Microsoft.Compute/virtualMachineScaleSets/virtualMachines`) built from a single VM profile
    - All instances are identical in size and operating system
    - Required for Azure Kubernetes Service (AKS) and Service Fabric
    - Supports automatic OS image upgrades and overprovisioning
    - Does not support Azure Backup or Azure Site Recovery
    - Up to 100 instances with fault domain guarantees, 1000 with multiple placement groups

## Availability

- Availability Zones (AZs): instances can be spread across 1, 2 or 3 zones in a region
- Fault Domains (FDs): instances are spread across separate racks (power and network) within a zone or region
- Max spreading: Azure spreads instances across as many fault domains as possible (`platformFaultDomainCount = 1`)
- Fixed spreading: instances are spread across a set number of fault domains (typically 2-3 in a region)
- SLA:
    - 99.95% for instances spread across fault domains in a single region
    - 99.99% for instances spread across multiple Availability Zones

## Load Balancer

- A Load Balancer can be associated with a Scale Set
- This will allow us to:
    - Evenly distribute our VMs across multiple Availability Zones to make your application Highly Available
    - Use Load Balancer probe checks for more robust Health checks
- We have the choice between 2 different load balancers:
    - Application Gateway is an HTTP/HTTPS web traffic load balancer with URL-based routing, SSL termination, session persistence, and web application firewall 
    -Azure Load Balancer supports all TCP/UDP network traffic, port-forwarding, and outbound flows

## Scaling Policies

- A Scaling Policy determinies when a VM should be added/removed to meet a current capacity requirement
- Scale Out: when a instance should be added to the Scale Set to increase capacity, eg. when CPU Threshold (%) greater than X for Y minutes add X servers
- Scale In: when a instance should be removed from the Scale Set to decrease capacity, eg. when CPU Threshold (%) less than X for Y minutes remove X servers
- When we are creating a Scale Set we have very limited options for our Scaling Policy
- After we create our Scale Set we have a lot more options available to configure the rules of your Scaling Policy
- Built-in Host-based metrics:
    - Percentage CPU
    - Network In
    - Network Out
    - Disk Read Bytes
    - Disk Write Bytes
    - Disk Read Operations/Sec
    - Disk Write Operations/Sec
    - CPU Credits Remaining
    - CPU Credits Consumed
- Aggerates:
    - Average
    - Minimum
    - Maximum
    - Total
    - Last
    - Count
- Operators
    - Greater than
    - Greater than or equal to
    - Less than
    - Less than or equal to
    - Equal to
    - Not equal to
- Actions
    - Increase count by X
    - Increase percent by X%
    - Increase count to X
    - Decrease count by X
    - Decrease percent by X%
    - Decrease count to X
-  Additional Metrics: there are two ways to collect additional metrics:
    - Application Insights installs a small instrumentation package in the application that monitors the app and sends telemetry to Azure
        - Use for application metrics such as page load performance and session counts
    - Azure Diagnostics extension is an agent that runs inside a VM instance and saves performance metrics to Azure Storage
        - Use for more detailed host-based metrics
- Scaling can be triggered in three ways:
    - Manual: you set a fixed instance count yourself
    - Metric-based: rules react to host, application or custom metrics
    - Schedule-based: instance count changes at a set time or on a recurring profile, eg. business hours
- Minimum and maximum instance counts are set on the autoscale profile, autoscale never goes outside this range
- Cool-down period: the time autoscale waits after a scaling action before evaluating rules again, default is 5 minutes

### Scale-In Policy

- Scale-in policy determines which VM is removed to decrease the capacity of the scale set
- Default:
    - Deletes the VM with the highest instance ID
    - Balances deletions across Availability Zones (AZs) and Fault Domains (FDs)
- Newest VM:
    - Deletes the newest VM
    - Balances deletions across Availability Zones
- Oldest VM:
    - Deletes the oldest VM
    - Balances deletions across Availability Zones
- Instance Protection can be applied to individual instances to override the scale-in policy:
    - Protect from scale-in: the instance is never removed by a scale-in action
    - Protect from scale set actions: the instance is also excluded from upgrades and automatic repairs

### Update Policy

- Update policy determines how VM instances are brought up to date with the latest scale set model
- Automatic:
    - Instances start upgrading immediately in random order
- Manual:
    - Existing instances must be manually upgraded
- Rolling:
    - Upgrades roll out in batches with an optional pause
- Automatic OS upgrades can be enabled to safely and automatically upgrade the OS disk for all instances

## Health Monitoring

- Health monitoring determines whether a VM instance in a scale set is healthy or unhealthy
- Unhealthy instances can be automatically removed and replaced to maintain availability and reliability
- Health signals are used by:
    - Autoscaling decisions
    - Automatic repairs
    - Load balancing traffic distribution
- Health Monitoring methods:
    - Application Health Extension
        - Use the Application Health Extension
        - Sends HTTP/HTTPS requests to a specific endpoint on the VM
        - A response of HTTP 200 (OK) indicates the instance is healthy
        - Key configurations:
            - Protocol: HTTP/HTTPS
            - Port number: 80/443
            - Path: `/health`
        - Best suited for:
            - Application-level health checks
            - Verifying that the app (not just the VM) is running correctly
    - Load Balancer Health Probe
        - Uses an Azure Load Balancer health probe
        - Supports: TCP, HTTP, HTTPS
        - The scale set relies on the load balancer's health probe result to determine the VMs health
        - Only available with Uniform orchestration, Flexible orchestration must use the Application Health Extension
        - Commonly used when:
            - A load balancer is already required
            - Basic connectivity or service availability checks are sufficient

## Automatic Repair Policy

- Automatic repairs can be enabled at the scale set level
- When a VM instance is marked unhealthy:
    - The instance is deleted
    - A new instance is automatically created to replace it
- Key settings:
    - Grace Period (minutes): allows the VM to boot and initiate before health checks are enforced
- Automatic repairs rely on health monitoring being enabled

## Allocation Policy and Placement Groups

- A placement group is a construct similar to an availability set, with its own fault domains and upgrade domains
- By default a scale set consists of a single placement group with a maximum of 100 VMs
- If the scale set property `singlePlacementGroup` is set to `false`, the scale set can be composed of multiple placement groups and has a range of 0-1,000 VMs
- In the portal this is the `Enable scaling beyond 100 instances` setting, which is set to `Yes` when the instance count is above 100
- A large scale set (`singlePlacementGroup = false`) has the following requirements:
    - Managed disks are required
    - A Standard SKU Load Balancer is required, Basic SKU only works with a single placement group
    - InfiniBand networking is not supported
- Maximum instance counts:
    - Scale sets created from Marketplace or Azure Compute Gallery images can scale to 1,000 VMs
    - Scale sets created from custom images can scale to 600 VMs
- You can convert a scale set from a single placement group to multiple placement groups, but you cannot convert back
- Fault domains and upgrade domains are only consistent within a placement group

## Proximity Placement Groups

- A proximity placement group (PPG) is a logical grouping that keeps Azure compute resources physically close together in the same datacenter
- Used to reduce network latency between VMs, scale sets and availability sets that must talk to each other
- A proximity placement group is a different concept to a placement group, it controls physical locality rather than instance count limits
- Only supported when the scale set uses a single Availability Zone or no zone at all
- For Uniform orchestration the PPG can be changed after deployment by stopping all instances, for Flexible orchestration it cannot be changed after deployment

## Overprovisioning

- Only available with Uniform orchestration, and enabled by default
- The scale set temporarily creates more VMs than requested, then deletes the extra instances once enough have been provisioned successfully
- Improves deployment reliability and reduces deployment time
- You are not charged for the extra instances
- Allow around 20% extra subnet address space because a scale set uses a single subnet
