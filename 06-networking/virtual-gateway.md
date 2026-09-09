# Virtual Network Gateways

## VPN

- VPN (Virtual Private Network): provides encrypted connectivity across a shared or public network
- Azure VPN Gateway connects on-premises networks or individual clients to an Azure virtual network (VNet), or connects Azure VNets to each other

## Virtual Network Gateway

- A virtual network gateway consists of two or more Azure-managed virtual machines (VMs) that run routing and gateway services
- Azure deploys and manages these VMs in a dedicated subnet named `GatewaySubnet`
- Gateway type determines the service:
    - `Vpn`: provides encrypted VPN connections
    - `ExpressRoute`: connects a VNet to an ExpressRoute circuit using private connectivity that is not encrypted by default
- A VNet can have one VPN gateway and one ExpressRoute gateway, which can coexist
- Multiple VPN connections share the available bandwidth of the VPN gateway

## Gateway Subnet

- The subnet must be named exactly `GatewaySubnet` and belong to the VNet address space
- Use `/27` or larger, such as `/26`, for non-Basic gateway SKUs (stock keeping units); Basic supports smaller subnets
- Reserve sufficient addresses for future gateway configurations and upgrades
- Do not deploy application VMs or other workloads into `GatewaySubnet`
- Do not associate a network security group (NSG) with `GatewaySubnet`
- Do not add a user-defined route (UDR) with destination `0.0.0.0/0` to `GatewaySubnet`
- Keep gateway route propagation enabled on any route table associated with `GatewaySubnet`

## VPN Gateway Designs

- Site-to-Site (S2S): connects an on-premises network to a VNet through an on-premises VPN device
- Multi-Site: connects multiple on-premises networks to the same Azure VPN gateway using separate S2S connections
- Point-to-Site (P2S): connects an individual client computer to a VNet using VPN client software
- VNet-to-VNet: connects two VNets through VPN gateways, including VNets in different regions or subscriptions
- S2S and VNet-to-VNet use IPsec (Internet Protocol Security) and IKE (Internet Key Exchange) tunnels
- Use non-overlapping address spaces for connected networks in a standard configuration

## Site-to-Site Resources

- Virtual network gateway: the Azure end of the VPN tunnel
- Public IP address: the Azure VPN endpoint used by the on-premises device for an internet-based S2S connection
- Local network gateway: an Azure resource representing the on-premises VPN device and network, not a gateway VM deployed on-premises:
    - Contains the device public IP address or FQDN (fully qualified domain name) and on-premises address prefixes
    - Can include BGP (Border Gateway Protocol) peer settings for dynamic route exchange
- Connection: links the virtual network gateway and local network gateway using the S2S IPsec connection type
- Configure a compatible on-premises VPN device and matching shared keys and IPsec/IKE settings on both ends
- For multiple sites, create a local network gateway and connection for each site
- A standard VNet-to-VNet connection references the two virtual network gateways directly and does not require local network gateway resources

## VPN Types

- Route-based: uses routing tables to direct traffic into VPN tunnels:
    - Preferred for most deployments
    - Required for P2S, multi-site and VNet-to-VNet configurations
    - Supports BGP on supported gateway SKUs; route-based does not mean BGP is mandatory
- Policy-based: selects traffic using address-based IPsec policies:
    - Supported only on the Basic SKU and uses IKEv1
    - Limited to a single S2S tunnel and does not support P2S or BGP
- Changing between policy-based and route-based requires deleting and recreating the gateway

## Point-to-Site Configuration

- Requires a route-based VPN gateway; no on-premises VPN device or local network gateway is needed
- Configure a client address pool that does not overlap with the VNet, connected on-premises networks or client local networks
- Tunnel protocols include OpenVPN, IKEv2 and SSTP (Secure Socket Tunneling Protocol); support depends on the client and gateway SKU
- SSTP is supported only on Windows clients
- Authentication options:
    - Azure certificate authentication: upload the trusted root certificate public key to Azure and install a client certificate with its private key on each client
    - Microsoft Entra ID: requires OpenVPN and Azure VPN Client; supports Conditional Access and multifactor authentication (MFA)
    - RADIUS (Remote Authentication Dial-In User Service): forwards authentication to a reachable RADIUS server
- Generate and download the VPN client profile, then configure the client with the selected authentication method

## Availability and Gateway Sizing

- Active-standby: one gateway instance handles traffic while the other takes over during maintenance or failure
- Active-active: both gateway instances establish tunnels, each using its own public IP address; configure the on-premises device to connect to both
- Zone-redundant gateways distribute instances across availability zones in supported regions
- Choose the gateway SKU based on aggregate throughput, connection limits, availability and required features
- Use the current `VpnGw*AZ` SKUs for new production deployments; Basic has limited features and is intended for development and testing

## VNet Peering and Gateway Transit

- VNet peering connects VNets over the Microsoft backbone without requiring VPN gateways
- Gateway transit lets a peered spoke VNet use a hub VNet's gateway to reach on-premises networks:
    - Enable `Allow gateway transit` on the hub-to-spoke peering
    - Enable `Use remote gateways` on the spoke-to-hub peering
    - The spoke cannot have its own virtual network gateway when using a remote gateway
    - A VNet can use a remote gateway from only one peering
- Gateway transit is not supported by the Basic VPN Gateway SKU
- Gateway-learned routes propagate to spoke subnets unless route propagation is disabled on their route tables
- Peering is not transitive; gateway transit does not automatically enable communication between all spokes
- Workload subnet routes to on-premises prefixes can use the `Virtual network gateway` next-hop type

## Troubleshooting

- Check the connection status and tunnel ingress and egress metrics
- Verify device endpoint addresses, shared keys and matching IPsec/IKE settings
- Check for overlapping address spaces, missing routes and incorrect local network gateway prefixes
- Inspect effective routes and effective NSG rules on the workload network interface
- Use Azure Network Watcher VPN troubleshoot and VPN Gateway diagnostic logs to investigate tunnel failures
- For P2S, verify the client profile, certificates or identity settings, client address pool and routes