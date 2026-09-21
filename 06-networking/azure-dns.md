# Azure DNS

- Azure DNS is a hosting service for DNS domains that provides name resolution by using Microsoft Azure infrastructure
- We can make 2 types of DNS:
    - Public DNS Internet-facing: 
        - Allows us to manage domains for internet accessible domains
    - Private DNS Internal-facing
        - Allow us to use our own custom domains instead of the Azure provided domains
- We can't use Azure DNS to buy a domain name
- We can purchase a domain in App Services or a third-party provider and have Azure DNS manage

## Zones, Records and Record Sets

- DNS zone: a container for all DNS records for a specific domain name
- DNS record: an entry that provides information about a domain name, such as where its traffic should be directed
- A DNS record is composed of:
    - Name: identifies the host or domain to which the record applies, such as `www`
    - Type: determines how the record is handled, such as an A record that maps a name to an IPv4 address
    - Value: contains the record data, such as the IPv4 address `104.194.51.120`
- Record set: a collection of DNS records in a zone that have the same name and record type
- Azure DNS creates records as record sets, including record sets that contain only one record
- A Name Server (NS) record set can contain multiple authoritative name servers
- If one authoritative name server is unavailable, DNS resolvers can query another server listed in the NS record set

## Public Zone Delegation

- Creating a public zone does not automatically make Azure DNS authoritative for the domain
- Update the domain registrar's name server settings to use the name servers assigned to the Azure DNS zone
- Delegate a child zone by adding its NS records to the parent zone
- Record types:
    - A: maps a name to an IPv4 address
    - AAAA: maps a name to an IPv6 address
    - CNAME: maps an alias to another hostname
    - MX: identifies mail servers and their priorities
    - TXT: stores text such as domain verification information
    - PTR: supports reverse lookup from an IP address to a hostname
    - SOA (Start of Authority): stores zone authority and administrative information
- `@` represents the zone apex, such as `contoso.com`
- A CNAME cannot be created at the zone apex or coexist with other record types at the same name
- Azure DNS alias record sets can reference supported Azure resources, including public IP addresses and Traffic Manager profiles
- An A or AAAA alias can support a zone-apex mapping where a CNAME cannot be used
- TTL (time to live): period during which a resolver can cache a record before querying again
- Record changes can remain unseen until an existing cached value expires

## Private DNS Zones

- A private DNS zone is resolved from linked virtual networks, not through public internet delegation
- A virtual network link enables name resolution for resources using the appropriate Azure DNS resolution path
- Autoregistration automatically manages DNS records for supported VM interfaces in a linked virtual network
- A virtual network can have autoregistration enabled for only one private zone
- The same virtual network can have resolution links to multiple private zones without autoregistration
- Multiple virtual networks can link to the same private zone
- VNet peering does not automatically link private DNS zones or enable DNS resolution across peered networks
- A DNS zone link does not create network connectivity; routing and access rules must also allow the connection

## Private Endpoints and Hybrid Resolution

- Private endpoints require the service's normal hostname to resolve to a private IP from the client network
- Example storage private zone: `privatelink.blob.core.windows.net`
- A private DNS zone group associates a private endpoint with the relevant zones and manages its records
- Link the private zone to every client virtual network that needs direct Azure-provided resolution
- For custom DNS servers, configure forwarding through Azure DNS Private Resolver or a suitable DNS forwarder
- Azure DNS Private Resolver inbound endpoints let connected on-premises networks query Azure private zones
- Outbound endpoints and forwarding rulesets let Azure clients resolve designated domains through external DNS servers
- To troubleshoot, check the queried hostname, returned IP address, zone records, virtual network links and DNS forwarding