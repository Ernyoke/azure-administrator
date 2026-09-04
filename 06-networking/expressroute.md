# Azure ExpressRoute

- Azure ExpressRoutes creates private connections between Azure datacenters and infrastructure on our premises or in a colocation environment
- ExpressRoute connections don't traverse the public Internet, ensuring enhanced reliability, faster speeds, consistent latencies, and heightened security compared to traditional internet connections
- Connectivity options include:
    - Any-to-any (IP VPN) network
    - A point-to-point Ethernet network
    - Virtual cross-connection through a connectivity provider at a colocation facility
- Ideal for hybrid solutions with massive amounts of data or where latency matters
- Offers a 99.95% SLA on the circuit, but only when both redundant connections are used
- Traffic is not encrypted by default; IPsec or MACsec must be layered on top if encryption is required

## ExpressRoute Circuits

- Circuit: a logical connection between on-premises and Azure through a connectivity provider
- Every circuit always has two connections to two MSEE (Microsoft Enterprise Edge) routers for redundancy
- Service key: the GUID that identifies the circuit and is shared with the connectivity provider to provision it
- Provider status must be Provisioned and circuit status Enabled before peerings can carry traffic
- Bandwidths range from 50 Mbps to 10 Gbps, and can be increased on an existing circuit but never decreased
- Billing models:
    - Metered: lower monthly fee, outbound data is charged per GB
    - Unlimited: higher monthly fee, no charge for outbound data
- Inbound data transfer is always free

## Peering Types

- A circuit can have multiple peerings that share the same bandwidth
- Private peering: connects to VNets using private IP addresses, used for VMs and other resources
- Microsoft peering: connects to Microsoft public services such as Microsoft 365, Dynamics 365 and Azure PaaS services over public IP addresses
- Public peering is deprecated and replaced by Microsoft peering
- Each peering needs a VLAN ID, an ASN (Autonomous System Number) and a pair of /30 subnets for the primary and secondary links
- Routes are exchanged dynamically with BGP (Border Gateway Protocol)

## SKUs

- Local: connects only to Azure regions within the same metro area, included data transfer, cheapest option
- Standard: connects to any region within the same geopolitical area
- Premium: adds global connectivity to any region worldwide, more routes and more VNet links per circuit

## Gateways and Connectivity

- A virtual network gateway of type ExpressRoute is required in the VNet to link it to a circuit
- Gateway SKUs are Standard, HighPerformance, UltraPerformance and the ErGw1AZ, ErGw2AZ and ErGw3AZ zone-redundant equivalents
- The gateway needs a dedicated GatewaySubnet, recommended /27 or larger
- ExpressRoute and site-to-site VPN gateways can coexist in the same VNet for failover
- FastPath sends data directly to VMs bypassing the gateway to reduce latency, but the gateway must still exist
- Global Reach links two ExpressRoute circuits so on-premises sites can exchange traffic through the Microsoft network
- ExpressRoute Direct provides dedicated 10 Gbps or 100 Gbps port pairs into the Microsoft network without a connectivity provider