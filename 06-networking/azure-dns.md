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