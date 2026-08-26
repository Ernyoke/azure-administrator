# Virtual Network

- Azure Virtual Network (VNet): logically isolated private network in Azure where resources can communicate securely with each other, the internet and on-premises networks

## VNet Components

- Address space: range of private IP addresses available to the VNet
- Subnet: segment of a VNet address space used to organize and isolate resources
- Route table: collection of routes that controls where network traffic is directed

## Network Services

- Azure DNS: hosts public DNS zones and provides private DNS zones for name resolution within and between VNets
- Network Security Group (NSG): stateful packet filter that allows or denies inbound and outbound traffic at the subnet or network interface level
- Azure ExpressRoute: private connection between an on-premises network and Microsoft cloud services, with circuit bandwidths from 50 Mbps to 10 Gbps
- Azure Virtual WAN: centralized networking service for connecting and routing traffic between branches, VNets and remote users
- Virtual network gateway: Azure-managed gateway used by VPN Gateway or ExpressRoute to connect a VNet to other networks

## Network Interfaces

- Azure network interface (NIC): virtual network adapter that connects an Azure virtual machine (VM) to a VNet
- Every Azure VM requires at least one NIC for network communication
- The VM size determines how many NICs can be attached to a VM
- All NICs attached to a VM must connect to the same VNet but can connect to different subnets in that VNet
- A NIC has one or more IP configurations
- Each IP configuration has one private IP address and can have one public IP address
- All IP configurations on a NIC use the same subnet
- Each Azure NIC can have:
    - An optional Network Security Group (NSG)
    - Custom DNS server settings
    - IP forwarding enabled for a network virtual appliance (NVA)

## OSI Model Association

- Layer 1, Physical layer: cables, signals, connectors and physical data transmission
- Layer 2, Data Link layer: MAC addresses, Ethernet frames and switching
- Layer 3, Network layer: logical addressing and routing using IP
- Switches primarily operate at Layer 2
- Routers operate at Layer 3

## VNet Peering

- VNet peering: connects two VNets so their resources can communicate directly over the Microsoft backbone network
- Peered VNets remain separate resources and their address spaces cannot overlap
- VNet peering types:
    - Regional VNet peering: connects VNets in the same Azure region
    - Global VNet peering: connects VNets in different Azure regions
- VNet peering is non-transitive, so each pair of VNets that requires direct connectivity must be peered
- Peering can connect VNets across subscriptions and Microsoft Entra tenants

## Network Routes and Route Tables

- Route: defines where traffic for an address prefix is sent
- Route table: collection of routes that determines the next hop for traffic leaving a subnet
- Azure automatically routes traffic between subnets within a VNet
- Effective routes combine system routes, user-defined routes and routes propagated through Border Gateway Protocol (BGP)

## Default System Routes

- Azure automatically creates and manages system routes for each subnet
- System routes cannot be created or deleted but can be viewed in a network interface's effective routes
- Key default system routes:
    - VNet address prefixes: Virtual network next hop
    - `0.0.0.0/0`: Internet next hop
    - `10.0.0.0/8`: None next hop
    - `172.16.0.0/12`: None next hop
    - `192.168.0.0/16`: None next hop
    - `100.64.0.0/10`: None next hop (reserved for Carrier-Grade NAT)
- Azure also creates None routes for additional Microsoft-reserved address prefixes
- None next hop drops traffic unless a more specific route matches the destination
- When a reserved private range is assigned to the VNet, Azure uses a Virtual network route for that address space
- Azure can add system routes when capabilities such as VNet peering, virtual network gateways or service endpoints are enabled

## User-Defined Routes

- User-defined route (UDR): custom route used to override or supplement Azure system routes
- UDRs are created in a route table and take effect when the route table is associated with a subnet
- A route table can be associated with multiple subnets
- A subnet can be associated with only one route table
- System routes remain active unless a UDR overrides them
- A UDR for `0.0.0.0/0` with a None next hop prevents the subnet from using the default route to the internet
- Azure selects the route with the longest matching address prefix
- For identical prefixes, the normal route-source priority is UDR, BGP route, then system route
- System routes for VNet, VNet peering and service endpoints are preferred over BGP routes
- Service endpoint routes cannot be overridden by a UDR

## Next-Hop Types

- Virtual network: routes traffic within the VNet address space
- Internet: routes traffic to the public internet
- None: drops traffic instead of forwarding it
- Virtual network gateway: routes traffic through a VPN gateway and is not supported as a UDR next hop for an ExpressRoute gateway
- Virtual appliance: routes traffic to a network virtual appliance (NVA), such as a firewall
- A virtual appliance next hop requires the private IP address of the appliance
- IP forwarding must be enabled on an NVA network interface and in its operating system or network application as required

## Address Spaces

- Address space: range of IP addresses allocated for use within a VNet
- CIDR (Classless Inter-Domain Routing) notation determines the number of addresses in a range, such as `10.0.0.0/24`
- A `/24` CIDR block contains 256 addresses
- A `/27` CIDR block contains 32 addresses
- A VNet can have multiple address spaces
- Address ranges within the same VNet cannot overlap
- Azure reserves five IP addresses in each subnet, so the total address count is not the usable address count
- Azure reserves the first four and last IP addresses in each subnet:
    - First address: network address
    - Second address: default gateway
    - Third and fourth addresses: Azure DNS mapping
    - Last address: network broadcast address
- A `/24` subnet has 251 usable addresses and a `/27` subnet has 27 usable addresses

## Subnets

- Subnet: logical division of a VNet address space used to isolate and manage workloads
- An IP-enabled Azure resource is deployed into a subnet and receives a private IP address from that subnet
- Every subnet address range must be contained within a VNet address space
- Subnet address ranges within a VNet cannot overlap

### Route Table Association

- Every subnet uses Azure system routes by default and does not require a custom route table
- A route table can be associated with a subnet to apply user-defined routes
- A subnet can be associated with only one route table

### Public and Private Subnets

- A private subnet has `defaultOutboundAccess` set to `false`, which prevents VMs from using implicit default outbound access
- New VNets created with API versions released after March 31, 2026 use private subnets by default
- Existing VNets retain their current default outbound access configuration
- Resources in a private subnet can reach public endpoints through an explicit outbound method:
    - NAT Gateway
    - Standard Load Balancer outbound rules
    - Standard public IP address on a NIC
    - Azure Firewall or an NVA reached through a UDR
- Internet-initiated inbound access requires an explicit public ingress method and an NSG rule that allows the traffic
- The private subnet setting controls default outbound access and does not replace NSGs or route tables

### Network Security Group Association

- An NSG can be associated with a subnet to control inbound and outbound traffic
- An NSG can also be associated with a NIC, and traffic must be allowed by both NSGs when both associations are used
- NSG security rules allow or deny traffic based on:
    - Source and destination IP address or service tag
    - Source and destination port
    - Protocol
    - Direction
    - Priority
- NSGs are stateful, so return traffic for an allowed connection does not require a separate rule

### Gateway Subnet

- `GatewaySubnet`: specialized subnet reserved for Azure-managed virtual network gateway VMs and services
- The subnet must be named `GatewaySubnet`
- Virtual network gateway types that use `GatewaySubnet`:
    - VPN Gateway
    - ExpressRoute gateway
- A VNet can have one VPN gateway and one ExpressRoute gateway
- Do not deploy other resources or user workloads in `GatewaySubnet`
- Use a `/27` or larger gateway subnet for all non-Basic SKUs and to allow future gateway configurations
- Network Security Groups and UDRs with a `0.0.0.0/0` destination are not supported on `GatewaySubnet`

## Private Link

- Azure Private Link: provides private access from a VNet to Azure PaaS, customer-owned and partner services over the Microsoft backbone network
- Private Endpoint: network interface that uses a private IP address from a VNet to connect privately and securely to a service powered by Azure Private Link
- Third-party providers can make their services available through Azure Private Link
- Many Azure services support Azure Private Link, including Azure Storage, Azure Cosmos DB and Azure SQL Database
- A private endpoint does not automatically disable public access to the target service
- Private DNS is commonly used to resolve the service name to the private endpoint IP address
- Private Link service: makes your own workload privately available through Azure Private Link
- A standard Private Link service deployment references the frontend IP configuration of an Azure Standard Load Balancer
- Azure Basic Load Balancer is not supported by Private Link service
