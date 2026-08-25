# Virtual Network

- Azure Virtual Network (VNet): logically isolated section of the Azure network where Azure resources can communicate securely

## VNet Components

- Address space: range of private IP addresses available to the VNet
- Subnet: segment of a VNet address space used to organize and isolate resources
- Route table: collection of routes that controls where network traffic is directed

## Network Services

- Azure DNS: hosts and manages DNS domains using Azure infrastructure
- Network Security Group (NSG): virtual firewall that filters inbound and outbound traffic at the subnet or network interface level
- Azure ExpressRoute: private connection between an on-premises network and Microsoft cloud services, with circuit bandwidths from 50 Mbps to 10 Gbps
- Azure Virtual WAN: centralized networking service for connecting and routing traffic between branches, VNets and remote users
- Virtual network gateway: provides site-to-site VPN connectivity between a VNet and an on-premises network

## Network Interfaces

- Network interface: software or hardware interface that allows devices or network protocol layers to communicate
- Network interface controller (NIC): hardware component that connects a computer to a network
- Other names for a physical NIC:
    - Network interface card
    - Network adapter
    - LAN adapter
    - Physical network interface
- NIC functions:
    - Transmits and receives data frames over wired or wireless network media
    - Works with operating system network drivers to communicate using IP
    - Can be physical hardware or virtual software used by cloud virtual machines
- Azure network interface: virtual network adapter that connects an Azure virtual machine (VM) to a VNet
- Every Azure VM requires at least one NIC for network communication
- Supported VM sizes can use multiple NICs for different subnets, network isolation, virtual appliances and load balancing
- Each Azure NIC is associated with:
    - A private IP address
    - An optional public IP address
    - A VNet subnet
    - An optional Network Security Group (NSG)

## OSI Model Association

- Layer 1, Physical layer: cables, signals, connectors and physical data transmission
- Layer 2, Data Link layer: MAC addresses, Ethernet frames and switching
- Layer 3, Network layer: logical addressing and routing using IP
- Switches primarily operate at Layer 2
- Routers operate at Layer 3

## VNet Peering
- VNET peering is when you connect multiple VNet so they act as one network
- There are 2 types of VNet Peering:
    - Regional VNet Peering When you peer two VNets from the same region
    - Global VNet peering When you peer two VNets from two different regions

## Network Routes and Route Tables

- Route: defines where traffic for an address prefix is sent
- Route table: collection of routes used by a router or network host to determine the next hop for traffic
- Azure automatically routes traffic between subnets within a VNet

## Default System Routes

- Azure automatically creates and manages system routes for each subnet
- System routes are not visible as route table resources in the Azure portal
- Default system routes:
    - VNet address prefixes: Virtual network next hop
    - `0.0.0.0/0`: Internet next hop
    - `10.0.0.0/8`: None next hop
    - `172.16.0.0/12`: None next hop
    - `192.168.0.0/16`: None next hop
    - `100.64.0.0/10`: None next hop (reserved for Carrier-Grade NAT)
- None next hop drops traffic for address prefixes that are not assigned to the VNet
- Azure can add system routes when capabilities such as VNet peering, virtual network gateways or service endpoints are enabled

## User-Defined Routes

- User-defined route (UDR): custom route used to override or supplement Azure system routes
- UDRs are created in a route table and take effect when the route table is associated with a subnet
- A route table can be associated with multiple subnets
- A subnet can be associated with only one route table
- System routes remain active unless a UDR overrides them
- A UDR for `0.0.0.0/0` with a None next hop prevents the subnet from using the default route to the internet

## Next-Hop Types

- Virtual network: routes traffic within the VNet address space
- Internet: routes traffic to the public internet
- None: drops traffic instead of forwarding it
- Virtual network gateway: routes traffic through a VPN or ExpressRoute gateway
- Virtual appliance: routes traffic to a network virtual appliance (NVA), such as a firewall
- A virtual appliance next hop requires the private IP address of the appliance

## Address Spaces

- Address space: range of IP addresses allocated for use within a VNet
- CIDR (Classless Inter-Domain Routing) notation determines the number of addresses in a range, such as `10.0.0.0/24`
- A `/24` CIDR block contains 256 addresses
- A `/27` CIDR block contains 32 addresses
- A VNet can have multiple address spaces
- Address ranges within the same VNet cannot overlap
- Azure reserves five IP addresses in each subnet, so the total address count is not the usable address count
- For a subnet range such as `x.x.x.0/24`, Azure reserves:
    - `x.x.x.0`: network address
    - `x.x.x.1`: default gateway
    - `x.x.x.2` and `x.x.x.3`: Azure DNS mapping
    - `x.x.x.255`: network broadcast address
- The network and broadcast addresses depend on the subnet range and are not always `.0` and `.255`

## Subnets

- Subnet: logical division of a VNet address space used to isolate and manage workloads
- An IP-enabled Azure resource is deployed into a subnet and receives a private IP address from that subnet
- Subnet address ranges within a VNet cannot overlap

### Route Table Association

- Every subnet uses Azure system routes by default and does not require a custom route table
- A route table can be associated with a subnet to apply user-defined routes
- A subnet can be associated with only one route table

### Public and Private Subnets

- Public and private describe whether resources in a subnet can reach or be reached from the internet
- Azure does not natively classify subnets as public or private
- A public IP address or public load balancer is required for unsolicited inbound internet access
- To restrict outbound internet access, override or remove internet-bound routes and use Network Security Group (NSG) rules or Azure Firewall as required

### Network Security Group Association

- An NSG can be associated with a subnet to control inbound and outbound traffic
- NSG security rules allow or deny traffic based on:
    - Source and destination IP address or service tag
    - Source and destination port
    - Protocol

### Gateway Subnet

- `GatewaySubnet`: specialized subnet used by an Azure virtual network gateway
- The subnet must be named `GatewaySubnet`
- Azure deploys and manages the gateway instances in this subnet
- User virtual machines must not be deployed in `GatewaySubnet`
