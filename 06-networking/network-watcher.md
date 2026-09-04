# Azure Network Watcher

- Azure Network Watcher provides tools to monitor, diagnose, view metrics, and enable or disable logs for Azure IaaS networking resources
- Network Watcher is a regional service and only works with resources in the same region
- Network Watcher is enabled automatically in a region when a virtual network is created or updated in that region
- Enabling Network Watcher creates a resource in a resource group named NetworkWatcherRG which is created automatically
- Automatic enablement can be opted out per subscription, after which Network Watcher must be enabled manually per region
- Network Watcher monitors IaaS resources such as:
    - Virtual machines
    - Virtual networks
    - Application gateways
    - Load balancers
    - VPN gateways
- Network Watcher cannot be used to monitor PaaS (fully managed services) or web analytics
- Tools are grouped into three categories:
    - Monitoring: topology, connection monitor
    - Network diagnostic tools: IP flow verify, NSG diagnostics, next hop, effective security rules, connection troubleshoot, packet capture, VPN troubleshoot
    - Traffic: flow logs, traffic analytics
- Most VM-level tools require the Network Watcher Agent VM extension to be installed on the target VM

## Topology

- Generates a visual diagram of the resources in a virtual network and the relationships between them
- Scoped to a subscription, resource group, or virtual network
- The diagram can be downloaded as an SVG to use in presentations and documentation

## Connection Monitor

- Connection Monitor: unified end-to-end connection monitoring for Azure and hybrid endpoints
- Replaces the retired Network Performance Monitor (NPM) and Connection Monitor (classic)
- Requires a Log Analytics workspace to store the data
- Requires the Network Watcher Agent extension on Azure VMs and the Azure Monitor Agent on on-premises machines
- Monitors:
    - Reachability, latency, and percentage of packet loss between endpoints
    - Changes in the network topology and hop-by-hop latency along the path
    - Connectivity to Azure PaaS endpoints, URLs, and IP addresses
    - The performance of Azure ExpressRoute
- Supports TCP, ICMP, and HTTP test protocols
- Generates alerts when a threshold for latency, packet loss, or failed checks is breached
- Detects network issues such as traffic blackholing, routing errors, and DNS resolution failures

## IP Flow Verify

- Checks whether a packet is allowed or denied to or from a VM based on network security group (NSG) rules
- Requires the 5-tuple: protocol, direction, local IP and port, remote IP and port
- Returns allow or deny and the name of the NSG rule that made the decision
- Does not evaluate Azure Firewall, user-defined routes, or in-guest firewalls

## NSG Diagnostics

- Evaluates all NSGs and Azure Virtual Network Manager security admin rules in the path of a packet
- Supports sources and destinations such as VMs, subnets, and IP addresses
- Returns the traffic verdict and the full list of rules that were applied

## Next Hop

- Shows the next hop type and IP address for traffic leaving a VM to a specified destination
- Used to detect incorrect or missing user-defined routes (UDR)
- Next hop types include:
    - Internet
    - VirtualNetwork
    - VirtualNetworkGateway
    - VirtualAppliance
    - VnetLocal
    - VnetPeering
    - None: the traffic is dropped

## Effective Security Rules

- Shows all NSG rules applied to a network interface, combining the rules from the NIC-level and subnet-level NSGs
- Used to confirm which rule wins when NSGs exist at both levels
- Inbound traffic is evaluated at the subnet NSG first and then the NIC NSG, outbound traffic is evaluated in the reverse order

## Connection Troubleshoot

- Tests a TCP or ICMP connection from a source VM or application gateway to a destination VM, FQDN, URI, or IP address
- Returns the connectivity status, latency, number of failed probes, and the hop-by-hop path
- Identifies the resource that is blocking the traffic, such as an NSG rule or a UDR
- Provides a point-in-time test, unlike Connection Monitor which runs continuously

## Packet Capture

- Captures traffic to and from a VM or a virtual machine scale set and stores it as a .cap file
- Requires the Network Watcher Agent VM extension on the target VM
- Capture files can be stored in a storage account, on the VM local disk, or both
- Filters can limit the capture by protocol, local and remote IP address, and port
- Limits can be set on the capture duration, the maximum bytes per packet, and the total bytes per session
- Can be triggered automatically by an alert using an Azure Automation runbook

## VPN Troubleshoot

- Diagnoses virtual network gateways and their connections
- Requires a storage account to store the diagnostic logs and results
- Returns a healthy or unhealthy status with the detected fault and the recommended action
- Detects issues such as mismatched pre-shared keys, IKE configuration mismatches, and an unreachable on-premises device

## Flow Logs

- NSG flow logs record IP traffic flowing through a network security group
- Virtual network (VNet) flow logs record traffic at the virtual network level and are the recommended replacement for NSG flow logs
- Flow logs are stored in a storage account in JSON format
- Data is written every 60 seconds and retention can be set from 0 (forever) to 365 days
- Logged data includes source and destination IP and port, protocol, direction, decision (allow or deny), and flow state
- Version 2 logs add throughput information such as bytes and packets sent and received
- Requires the Microsoft.Insights resource provider to be registered on the subscription

## Traffic Analytics

- Traffic Analytics: cloud-based solution that analyzes flow logs to give visibility into user and application activity
- Requires flow logs to be enabled and a Log Analytics workspace
- The processing interval can be set to every 10 minutes or every 60 minutes
- Provides insight into:
    - Traffic hotspots and top talkers
    - Malicious traffic and blocked flows
    - Traffic distribution across regions, subnets, and virtual networks
    - Open ports and applications communicating over the internet
    - Network security group rule hits

## Usage and Quotas

- Shows how many network resources are deployed against the subscription limit per region
- Used to check quota consumption for resources such as virtual networks, public IP addresses, NSGs, and load balancers