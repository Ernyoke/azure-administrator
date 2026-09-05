# Azure Load Balancer

- Azure Load Balancer distributes network traffic across backend virtual machines (VMs) or virtual machine scale set (VMSS) instances
- Operates at layer 4 of the OSI (Open Systems Interconnection) model and load balances TCP and UDP traffic
- Uses load-balancing rules and health probes to direct new connections to healthy backend instances
- Uses pass-through load balancing, preserving the client's source IP address
- Does not provide URL-based routing, TLS (Transport Layer Security) termination, or a WAF (Web Application Firewall)
- Application Gateway provides layer 7 HTTP and HTTPS routing, TLS termination, and optional WAF protection

## Public and Internal Load Balancers

- Public load balancer:
    - Uses a public frontend IP address for internet-facing applications
    - Maps the frontend IP and port to a backend instance's private IP and port
    - Backend VMs do not need their own public IP addresses
    - Can provide outbound internet access through outbound rules
- Internal load balancer:
    - Uses a private frontend IP address from a virtual network (VNet) subnet
    - Accessible through private connectivity, such as VNet peering, VPN (Virtual Private Network), or ExpressRoute
    - Commonly used between application tiers, such as web servers and database servers
    - Does not provide outbound internet connectivity by itself

## SKUs

- SKU (Stock Keeping Unit): determines the load balancer's feature set
- Standard: supports public and internal load balancing, availability zones, outbound rules, and Azure Monitor metrics
- Gateway: used to insert third-party network virtual appliances (NVAs), such as firewalls, into a traffic path
- Basic: retired on September 30, 2025, existing deployments should migrate to Standard
- A Standard public load balancer requires a Standard public IP address
- Standard is secure by default, inbound traffic must be permitted by network security groups (NSGs) on backend subnets or network interfaces (NICs)
- NSGs apply to backend resources, not directly to the load balancer

## Core Components

- Frontend IP configuration: the public or private IP address that clients connect to
- Backend pool: the group of instances that receives load-balanced traffic
- Backend pool membership can use NIC IP configurations or backend IP addresses
- For a regional load balancer, backend instances belong to the same VNet and can span subnets and availability zones
- Health probe: checks whether a backend instance can accept new connections
- Load-balancing rule: connects a frontend IP, protocol, and port to a backend pool and backend port
- A load balancer can have multiple frontend IP configurations, backend pools, probes, and rules

## Health Probes

- Supported probe protocols are TCP, HTTP, and HTTPS
- TCP probe: checks whether a TCP connection can be established on the configured port
- HTTP or HTTPS probe: sends a request to the configured path and requires an HTTP 200 response
- Configure the probe port, interval, and failure threshold, plus a path for HTTP or HTTPS probes
- The probe port can differ from the application port, but it must reflect whether the application can serve traffic
- An unhealthy instance stops receiving new load-balanced connections until it passes its health checks again
- Standard Load Balancer allows established TCP connections to continue when a probe fails, including when all backends are unhealthy
- IPv4 probes originate from `168.63.129.16`, represented by the `AzureLoadBalancer` service tag
- The default `AllowAzureLoadBalancerInBound` NSG rule permits probes, but a higher-priority deny rule can block them
- Guest operating system firewalls must also allow probe traffic
- Allowing the `AzureLoadBalancer` service tag does not allow client application traffic, which needs separate NSG rules

## Load-Balancing Rules

- A rule specifies the frontend IP, frontend port, backend pool, backend port, protocol, and health probe
- Frontend and backend ports can differ, such as frontend TCP 80 mapping to backend TCP 8080
- Rules distribute connections across healthy instances, not individual HTTP requests within an existing connection
- Creating a load-balancing rule does not open the application port in an NSG or guest firewall
- TCP idle timeout controls how long an idle connection remains tracked
- TCP reset on idle timeout can notify both endpoints that the connection has closed
- Floating IP changes the destination IP mapping behavior and is used for scenarios such as SQL Server availability group listeners
- Floating IP requires corresponding configuration inside the backend operating system
- HA (High Availability) ports: an internal Standard Load Balancer rule using protocol All and port 0 to load balance all TCP and UDP ports, commonly used for NVAs

## Session Persistence

- Default distribution uses a five-tuple hash:
    - Source IP address
    - Source port
    - Destination IP address
    - Destination port
    - Protocol
- None: uses the five-tuple hash, so separate connections from the same client can reach different backends
- Client IP: uses a two-tuple hash of source IP and destination IP
- Client IP and protocol: uses a three-tuple hash of source IP, destination IP, and protocol
- Client IP persistence is also called source IP affinity and is not cookie-based
- Backend health or pool membership changes can change which instance receives a client's new connections

## Inbound NAT Rules

- NAT (Network Address Translation): maps incoming frontend traffic to a particular backend instance
- Load-balancing rule: distributes traffic across a pool of healthy instances
- Inbound NAT rule: forwards traffic to a specific instance instead of load balancing it
- Example: frontend TCP port 50001 forwards to TCP port 22 on one VM for SSH access
- Inbound NAT rules can allocate frontend ports from a range to instances in a backend pool
- Health probes do not control inbound NAT forwarding
- NSGs and guest firewalls must allow the destination port, use Azure Bastion when public management port exposure is unnecessary

## Outbound Connectivity

- Outbound rules on a Standard public load balancer provide explicit SNAT (Source Network Address Translation) for backend instances
- SNAT translates backend private IP addresses to a frontend public IP address for outbound connections
- An outbound rule specifies the backend pool, frontend IP configuration, protocol, and outbound port allocation
- SNAT ports are finite, exhaustion can cause new outbound connections to fail
- Mitigations include reusing connections, increasing allocated ports, or adding frontend public IP addresses
- When using an outbound rule with a frontend also used for inbound load balancing, disable automatic outbound SNAT on the load-balancing rule
- NAT Gateway is the recommended option for scalable outbound internet connectivity and takes precedence for new outbound connections when attached to the backend subnet
- Configure an explicit outbound method rather than relying on default outbound access

## Availability Zones

- Standard Load Balancer supports zonal and zone-redundant frontends in regions with availability zones
- Zonal frontend: tied to a specific availability zone
- Zone-redundant frontend: can remain available if an availability zone fails
- A zone-redundant frontend does not make backend VMs zone-redundant, distribute healthy backend instances across zones too
- Frontend zone configuration and backend instance placement are separate choices

## Monitoring and Troubleshooting

- Azure Monitor metrics include:
    - Health Probe Status: whether backend instances respond successfully to probes
    - Data Path Availability: availability of the load balancer's data path
    - Byte Count, Packet Count, and SYN Count: traffic volume and new TCP connection activity
    - Used SNAT Ports and SNAT Connection Count: outbound port usage and connection activity
- When clients cannot connect:
    - Confirm the frontend IP, protocol, ports, and backend pool in the rule
    - Confirm backend VMs are running and the application listens on the backend port
    - Check probe status, probe port, and HTTP or HTTPS response code and path
    - Check effective NSG rules and guest firewalls for both client and probe traffic
    - Check effective routes and user-defined routes for incorrect next hops or asymmetric routing
    - Use Network Watcher connection troubleshoot, IP flow verify, or packet capture to isolate the failure
- Healthy probes do not guarantee client connectivity, probes and client connections can use different ports and security rules
- For uneven distribution, check session persistence and test multiple connections rather than repeatedly using one persistent connection
- For outbound failures, check the configured outbound method and SNAT port usage