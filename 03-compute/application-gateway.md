# Application Gateway

- Azure Application Gateway is an application-level routing and load-balancing service
- Application Gateway works with HTTP and HTTPS requests
- Offers advanced routing rules based on the content of the request
- Azure Web Application Firewall (WAF) policies can be attached to an application gateway to provide additional security

## Application Gateway - Frontends and Backends

- To configure an application gateway, set up a frontend, a backend, and routing rules
- Frontend configuration:
    - Private IP provides an internal frontend
    - Public IP provides an internet-facing frontend
- Backend configuration:
    - A backend pool is a collection of resources to which the application gateway can send traffic
    - A backend pool can contain:
        - Virtual machines
        - Virtual machine scale sets
        - IP addresses
        - Domain names
        - App Service
- Routing rules:
    - Listeners: listen for incoming traffic and pass it to rules
    - Rules: specify where traffic should be sent
    - HTTP settings: specify how HTTP and HTTPS requests should be handled

## Listeners

- Listener: logical entity that checks for incoming connection requests
- A listener is configured with:
    - Frontend IP address
    - Frontend port
    - Protocol: HTTP or HTTPS
    - Host name: optional value used to identify a specific website
- Basic listener: accepts any request that matches the configured frontend IP, port, and protocol
- Multi-site listener: uses the host header to route requests for multiple websites through one application gateway
    - Supports multiple host names and wildcard host names
- HTTPS listener: requires a Transport Layer Security (TLS) certificate to terminate TLS at the application gateway
- Server Name Indication (SNI): allows multiple HTTPS listeners with different certificates to share the same frontend IP address and port
- Each listener is associated with a request routing rule that determines how matching requests are handled

## Routing Rules

- Backend HTTP settings configure how Application Gateway connects to backend pool members:
    - Backend port: port on which the backend servers listen for incoming traffic
    - Cookie-based affinity: uses cookies to keep a user session on the same backend server
    - Connection draining: gracefully removes backend pool members during planned service updates
    - Request timeout: number of seconds Application Gateway waits for a response from the backend pool before returning a 504 Gateway Timeout error
    - Override backend path: replaces the URL path so requests for one path can be routed to a different backend path
    - Override hostname: replaces the incoming HTTP host header for multi-tenant services such as App Service or API Management that require a specific host header or Server Name Indication (SNI)

