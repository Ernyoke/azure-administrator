# Azure Virtual WAN

- Azure Virtual WAN is a Microsoft-managed networking service that combines connectivity, security and routing through regional virtual hubs
- These functionalities include:
    - Automated branch connectivity through Software-Defined Wide Area Network (SD-WAN) partner devices and virtual private network (VPN) customer premises equipment (CPE)
    - Site-to-Site VPN connectivity
    - Remote user VPN connectivity (Point-to-Site)
    - Private ExpressRoute connectivity
    - Transitive connectivity between virtual networks
    - Transit connectivity between VPN and ExpressRoute branches
    - Azure Firewall integration in secured virtual hubs for traffic filtering and inspection, not tunnel encryption
- Azure Virtual WAN integrates with SD-WAN solutions to connect branches, datacenters and Azure virtual networks
- Virtual WAN types:
    - Basic: supports Site-to-Site VPN only, with no hub-to-hub connectivity
    - Standard: supports Site-to-Site VPN, Point-to-Site VPN, ExpressRoute, virtual network connections and hub-to-hub connectivity

## PoPs

- Point of Presence (PoP): a location where a provider offers access to its network, such as an internet service provider (ISP) datacenter or a Microsoft network edge site
- Hop: a forwarding step from one router to the next along a network path, not necessarily a change between networks
- Public internet routing is controlled by the networks carrying the traffic, so enterprises have limited control over the end-to-end path
- Private connectivity can offer more predictable performance, but is not automatically faster than internet connectivity

## MPLS

- Multiprotocol Label Switching (MPLS): a packet-forwarding method that uses labels instead of Layer 3 IP address lookups at each hop within an MPLS network
- Commonly used by service providers to connect datacenters and branch offices over a private provider network instead of the public internet
- Providers manage the network and offer performance, quality and availability commitments through service-level agreements (SLAs)
- How MPLS works:
    - An ingress router assigns a label as a packet enters the MPLS network
    - Label Switch Router (LSR): a router that forwards packets based on labels and swaps labels as packets move through the network
    - Labels identify forwarding treatment locally rather than encoding the destination address
    - Label Switched Path (LSP): an established path through the MPLS network that packets assigned to it follow
    - Paths can follow shortest routes or traffic-engineered routes based on network requirements
    - Labels are removed before the packet leaves the MPLS network, often with the outer label removed by the router before the egress router
    - Shim header: the MPLS label header inserted between the Layer 2 header and the Layer 3 packet
- Benefits:
    - Virtual Private Networks (VPNs): provide logically isolated private connectivity between sites over shared provider infrastructure
    - Traffic Engineering (TE): controls traffic paths to optimize network resource use and avoid congestion
    - Quality of Service (QoS): prioritizes traffic classes to meet performance requirements for applications such as voice and video
- Limitations:
    - Typically more expensive than internet-based connectivity
    - Private connectivity does not mean encryption: MPLS is a forwarding mechanism with no inherent encryption
    - Use an additional encryption mechanism when confidentiality is required
    - Cloud access may still traverse the public internet unless private cloud connectivity is arranged
- Azure ExpressRoute can connect a provider's MPLS VPN to Microsoft cloud services without traversing the public internet

## SD-WAN

- Software-Defined Wide Area Network (SD-WAN): centrally manages WAN connectivity and selects traffic paths across available connections using application policies and network conditions
- Separates centralized management and control functions from packet forwarding on branch devices
- Can use internet, MPLS and cellular connections together, or reduce reliance on MPLS by using internet connectivity
- Can reduce connectivity costs and simplify branch deployment through centralized policies and automation
- Private Azure connectivity:
    - A connectivity provider can link an existing MPLS VPN to Microsoft through an ExpressRoute circuit
    - ExpressRoute private peering provides private access to Azure virtual networks without traversing the public internet
    - ExpressRoute is not itself a VPN tunnel and does not encrypt traffic by default
- Microsoft 365 connectivity:
    - Backhauling branch traffic through a central datacenter and ExpressRoute can add latency compared with local internet access
    - Local internet breakout can reach a nearby Microsoft Point of Presence (PoP) and reduce unnecessary detours
    - Microsoft generally recommends internet connectivity for Microsoft 365
    - ExpressRoute access to Microsoft 365 uses Microsoft peering and requires Microsoft authorization
- Traffic selection and security:
    - SD-WAN selects available links or tunnels based on policies and measurements such as latency, jitter and packet loss
    - Does not predetermine every hop through the public internet or directly control an ISP's routing
    - Secure SD-WAN deployments typically use encrypted tunnels such as IPsec (Internet Protocol Security), not HTTPS as the WAN security mechanism
    - Encryption depends on the solution and configuration, not on ISP support for HTTPS
- Azure Virtual WAN integration:
    - Virtual hub: a Microsoft-managed virtual network that acts as a regional connectivity and routing point
    - A virtual hub is not a customer-deployed virtual network with service endpoints and is distinct from a Microsoft edge PoP
    - Standard Virtual WAN hubs support Site-to-Site VPN, Point-to-Site VPN and ExpressRoute gateways, and connections to spoke virtual networks
    - Supported SD-WAN partner devices can automate branch VPN connectivity to a hub
    - Supported partner network virtual appliances (NVAs) can also be deployed in a hub for integrated SD-WAN connectivity
    - Standard Virtual WAN provides hub-to-hub connectivity over the Microsoft backbone for transit between branches and virtual networks
    - Routing policies and connectivity choices determine traffic paths, rather than guaranteeing the fastest route