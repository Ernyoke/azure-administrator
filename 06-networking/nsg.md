# Network Security Groups

- A network security group (NSG) filters network traffic to and from Azure resources in a virtual network (VNet)
- An NSG contains multiple security rules

## Security Rules

- Each security rule has the following properties:
    - Name: unique name within the NSG
    - Source or destination: IP address, Classless Inter-Domain Routing (CIDR) block, service tag or application security group (ASG)
    - Port range: single port or range of ports, such as `80` or `10000-10005`
    - Protocol: TCP, UDP, Internet Control Message Protocol (ICMP) or Any
    - Action: Allow or Deny
    - Priority: number from 100 through 4096, where a lower number has a higher priority
- Inbound rules apply to traffic entering the NSG
- Outbound rules apply to traffic leaving the NSG

## Default Security Rules

- Default rules cannot be deleted, but custom rules with a higher priority can override them
- Default inbound rules:
    - `AllowVNetInBound` at priority `65000`: allows traffic from the `VirtualNetwork` service tag to the `VirtualNetwork` service tag
    - `AllowAzureLoadBalancerInBound` at priority `65001`: allows traffic from the `AzureLoadBalancer` service tag
    - `DenyAllInBound` at priority `65500`: denies all other inbound traffic
- Default outbound rules:
    - `AllowVNetOutBound` at priority `65000`: allows traffic from the `VirtualNetwork` service tag to the `VirtualNetwork` service tag
    - `AllowInternetOutBound` at priority `65001`: allows traffic to the `Internet` service tag
    - `DenyAllOutBound` at priority `65500`: denies all other outbound traffic

## NSG Security Rule Logic

- Limits:
    - Up to 5,000 NSGs per region per subscription
    - Up to 1,000 security rules per NSG
- Priority:
    - Two security rules cannot have the same priority and direction
    - Rules are processed in priority order, with lower numbers processed first
    - Processing stops when a rule matches the traffic
- NSG rules evaluate traffic by using the five-tuple:
    - Source IP address
    - Source port
    - Destination IP address
    - Destination port
    - Protocol
- Flow records:
    - A flow record is created for an existing connection
    - Traffic is allowed or denied based on the connection state in the flow record
    - Flow records make NSGs stateful
- Statefulness:
    - Return traffic for an allowed flow is automatically permitted
    - An inbound rule is not required for responses to traffic initiated outbound
    - An outbound rule is not required for responses to traffic initiated inbound
    - A rule is required in the direction where communication is initiated
- Interruption:
    - Removing a rule that allowed a flow might not interrupt existing connections
    - Rule changes affect new connections
    - Existing flows are interrupted after the connection stops and remains idle in both directions for at least a few minutes

