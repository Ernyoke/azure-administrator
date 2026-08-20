# Azure Firewall

- Azure Firewall is a managed, cloud-based, fully stateful network security service
- Provides centralized traffic filtering for Azure virtual networks and subscriptions
- Inspects north-south traffic between Azure and external networks and east-west traffic between internal networks
- Includes built-in high availability and automatic scaling
- Denies traffic by default until an allow rule is configured
- Rules are stateful, so return traffic for an allowed connection is permitted automatically

## Deployment

- Can be deployed in:
    - A virtual network
    - An Azure Virtual WAN secured virtual hub
- A virtual network deployment requires a dedicated subnet named `AzureFirewallSubnet`
- `AzureFirewallSubnet` should use a `/26` or larger address range to provide enough addresses for scaling
- Do not deploy other resources in `AzureFirewallSubnet`
- Subnet-level network security groups (NSGs) are not supported on `AzureFirewallSubnet`
- A Standard SKU static public IP address is normally associated with the firewall
- The firewall private IP address is used as the next hop for user-defined routes (UDRs)
- Availability zones can provide zone-redundant deployment in supported regions
- A common design deploys the firewall in a hub virtual network and routes traffic from peered spoke virtual networks through it
- Deploying one firewall per region avoids cross-region latency in a hub-and-spoke design

## SKUs

- Basic:
    - Intended for small and medium-sized environments
    - Supports throughput up to 250 Mbps
    - Supports stateful filtering, application rules, network rules, source network address translation (SNAT) and destination network address translation (DNAT)
    - Supports threat intelligence in alert mode only
- Standard:
    - Includes Basic capabilities
    - Supports throughput up to 30 Gbps
    - Adds network-level fully qualified domain name (FQDN) filtering, DNS proxy, custom DNS and web categories
    - Supports threat intelligence alert and deny modes
- Premium:
    - Includes Standard capabilities
    - Supports throughput up to 100 Gbps
    - Adds Transport Layer Security (TLS) inspection for outbound traffic
    - Adds Intrusion Detection and Prevention System (IDPS)
    - Adds URL filtering and advanced web category filtering
- Azure Firewall pricing includes a fixed hourly deployment charge and a data processing charge

## Firewall Rules

- Rules can be configured directly as classic rules or managed through an Azure Firewall Policy
- Azure Firewall Policy organizes rules into:
    - Rule collection groups
    - Rule collections
    - Individual rules
- A rule collection contains rules of the same type and applies one action to them
- Priority values range from `100` to `65000`
- A lower number represents a higher priority
- Rules are terminating, so processing stops when traffic matches a rule
- Rule processing order is always:
    - DNAT rules
    - Network rules
    - Application rules
- Rule type order takes precedence over numeric priority across different rule types
- Threat intelligence filtering is evaluated before configured NAT, network and application rules
- A parent firewall policy is processed before its child policy regardless of priority values
- Unmatched traffic is denied by default

## NAT Rules

- NAT rules use DNAT to publish private resources for inbound connections
- A DNAT rule translates a firewall public IP address and port to a private destination IP address and port
- Traffic that matches a DNAT rule is translated and allowed without further network rule processing
- Use specific source addresses instead of wildcards when allowing inbound access
- Application rules are not used for inbound connections

## Network Rules

- Network rules provide Layer 3 and Layer 4 filtering
- Match traffic by:
    - Source IP address
    - Destination IP address or FQDN
    - Destination port
    - Protocol
- Support TCP, UDP, Internet Control Message Protocol (ICMP) and any IP protocol
- Use network rules for non-HTTP and non-HTTPS traffic such as Remote Desktop Protocol (RDP), Secure Shell (SSH) and DNS
- A matching network rule is processed before any application rule, even when the application rule has a higher numeric priority

## Application Rules

- Application rules provide Layer 7 outbound filtering
- Match allowed destinations by FQDN, FQDN tag, web category or URL when supported by the SKU
- Support HTTP, HTTPS and Microsoft SQL traffic
- HTTP rules use the Host header to identify the destination
- HTTPS rules use Server Name Indication (SNI) unless Premium TLS inspection is enabled
- FQDN tags represent groups of FQDNs for Microsoft services such as Windows Update
- Application rules are processed only when no network rule matches
- Use Azure Web Application Firewall (WAF) for specialized inbound HTTP and HTTPS application protection

## IP Groups and Tags

- IP Groups store reusable collections of IP addresses and CIDR ranges
- IP Groups can be referenced as sources or destinations in network and NAT rules
- Updating an IP Group updates every rule that references it
- Service tags represent groups of Azure service IP prefixes maintained automatically by Microsoft
- FQDN tags represent groups of FQDNs associated with Microsoft services

## Network Address Translation

- SNAT translates outbound connections to an Azure Firewall public IP address
- Azure Firewall automatically SNATs traffic sent to public IP addresses
- Azure Firewall does not SNAT traffic sent to private ranges defined by RFC 1918 and RFC 6598 by default
- Traffic processed by application rules is always SNATed
- Adding public IP addresses to the firewall increases the available SNAT port pool
- Azure NAT Gateway can be associated with `AzureFirewallSubnet` to provide more scalable outbound SNAT ports
- DNAT translates inbound connections from a firewall public IP address to a private destination

## Routing

- Workload subnets commonly use a UDR with:
    - Address prefix: `0.0.0.0/0`
    - Next hop type: virtual appliance
    - Next hop address: Azure Firewall private IP address
- Routes must provide symmetric traffic flow through the firewall
- Azure Firewall can route and filter traffic between spoke virtual networks when spoke subnet routes use the firewall as the next hop
- Virtual network peering alone does not automatically route spoke traffic through the firewall
- Route tables must be associated with the subnets whose traffic should be inspected

## Forced Tunneling

- Forced tunneling sends Internet-bound traffic to an on-premises firewall or another network virtual appliance (NVA)
- Can use a UDR or a Border Gateway Protocol (BGP) learned default route
- Requires the Azure Firewall management network interface
- The management interface uses a dedicated subnet named `AzureFirewallManagementSubnet`
- The management public IP address is used only by the Azure platform for firewall operations
- The tenant data path can operate without a public IP address when forced tunneling is enabled
- Internet-bound traffic is SNATed to an Azure Firewall private IP address before it reaches the next hop
- Inbound Internet DNAT is not supported when forced tunneling causes asymmetric routing

## Threat Intelligence

- Uses Microsoft threat intelligence feeds to identify traffic from or to known malicious IP addresses and domains
- Modes include:
    - Off: threat intelligence filtering is disabled
    - Alert only: logs suspicious traffic but allows it
    - Alert and deny: logs and blocks suspicious traffic
- Threat intelligence filtering has the highest rule processing priority
- Allowlist entries can exclude trusted IP addresses or FQDNs from threat intelligence filtering

## DNS Proxy

- DNS proxy makes Azure Firewall an intermediary for DNS queries from client virtual machines
- Clients send DNS queries to the firewall private IP address
- The firewall forwards queries to Azure DNS or configured custom DNS servers
- Helps the firewall and clients use consistent DNS results for FQDN-based network rules
- When DNS proxy is enabled, virtual network DNS settings should point clients to the firewall private IP address

## Firewall Policy and Firewall Manager

- Azure Firewall Policy is a separate resource that centrally defines firewall rules and settings
- A policy can be associated with multiple firewalls
- Policies can inherit rule collection groups from a parent policy
- Parent policy rules always take precedence over child policy rules
- Azure Firewall Manager centrally manages firewalls and policies across subscriptions and regions
- Firewall Manager supports virtual network firewalls and Azure Virtual WAN secured virtual hubs

## Monitoring

- Azure Monitor collects platform metrics automatically
- Resource logs must be enabled through diagnostic settings
- Diagnostic settings can send logs to:
    - Log Analytics workspace
    - Storage account
    - Event Hubs
- Resource-specific logs provide separate tables for network, NAT, application, threat intelligence, IDPS and DNS proxy events
- Log Analytics uses Kusto Query Language (KQL) to query firewall logs
- Azure Firewall Workbook provides visual analysis across one or more firewalls
- Activity logs record management operations such as creating, updating or deleting firewall resources and policies

## Azure Firewall vs Other Services

- Network security group:
    - Provides distributed Layer 3 and Layer 4 filtering at subnet or network interface level
    - Uses five-tuple rules and does not provide centralized application-level FQDN filtering
- Azure Firewall:
    - Provides centralized Layer 3 through Layer 7 filtering across virtual networks and subscriptions
    - Supports FQDN filtering, threat intelligence, NAT and centralized logging
- Azure Web Application Firewall:
    - Protects inbound HTTP and HTTPS applications from web exploits such as SQL injection and cross-site scripting
    - Is available with Azure Application Gateway and Azure Front Door
