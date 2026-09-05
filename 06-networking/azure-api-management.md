# Azure API Management

- Azure API Management (APIM): a managed service for publishing, securing, monitoring, and managing APIs
- Its API gateway sits between clients and backend services, routing requests and enforcing API policies
- Backends can run in Azure, on-premises, or other clouds
- API Management does not normally host the application's business logic, backend services implement the API
- API Management is not explicitly listed in the current AZ-104 objectives, focus on service selection, identity, networking, and monitoring concepts

## API Gateway vs Other Azure Services

- API Management: API authentication, subscription keys, rate limits, quotas, transformations, caching, and developer onboarding
- Application Gateway: regional layer 7 HTTP and HTTPS routing with optional WAF (Web Application Firewall) protection
- Azure Load Balancer: layer 4 TCP and UDP traffic distribution
- Azure Front Door: global HTTP and HTTPS routing, acceleration, and optional WAF protection
- Application Gateway or Front Door can sit in front of API Management to provide WAF protection
- API Management policies do not replace a WAF

## Core Components

- API gateway:
    - Receives API calls and forwards them to the appropriate backend
    - Enforces authentication, rate limits, transformations, and other configured policies
    - Emits request metrics, logs, and traces
- Management plane:
    - Used by administrators to configure APIs, products, policies, and service settings
    - Accessible through the Azure portal, Azure CLI, PowerShell, and management APIs
- Developer portal:
    - Allows API consumers to discover documentation, test operations, and request subscriptions
    - Availability and capabilities depend on the service tier
- Managed gateway: hosted and operated by Azure
- Self-hosted gateway: containerized gateway deployed near backends in on-premises or other cloud environments, with configuration managed through Azure API Management

## APIs and Backends

- API: a collection of operations exposed to consumers through a gateway URL
- Operation: an individual API action, such as `GET /orders` or `POST /orders`
- Backend: the service that processes forwarded API requests
- APIs can be defined manually or imported from definitions such as OpenAPI
- Existing Azure App Service, Azure Functions, and Logic Apps endpoints can be exposed through API Management
- Importing an API definition does not deploy its backend implementation
- Version: exposes a distinct API contract, commonly used for breaking changes
- Revision: allows changes to an existing API to be tested before making the revision current

## Products and Subscriptions

- Product: a package containing one or more APIs made available to consumers
- Protected product: requires an API Management subscription key
- Open product: does not require a subscription key, other authentication policies can still apply
- API Management subscription: grants access to a product, a specific API, or all APIs, depending on its scope
- An API Management subscription is separate from an Azure billing subscription
- Each subscription has primary and secondary keys, allowing one key to be rotated while clients use the other
- The default subscription key header is `Ocp-Apim-Subscription-Key`
- A subscription key identifies an API subscription, not an individual end user's identity
- Product visibility in the developer portal is controlled through groups

## Authentication and Secrets

- Client-to-gateway authentication and gateway-to-backend authentication are separate concerns
- JWT (JSON Web Token) validation checks token signatures, issuer, audience, expiry, and required claims as configured
- Use `validate-jwt` for JWT validation or `validate-azure-ad-token` for Microsoft Entra tokens
- Configuring OAuth 2.0 for the developer portal does not automatically enforce token validation at the gateway
- Managed identity allows API Management to obtain tokens for supported backends without storing application credentials
- The managed identity still needs the required permissions on the target resource
- Named values: reusable configuration values referenced by policies, including secrets and Key Vault-backed values
- Azure Key Vault can store secrets and certificates used by API Management
- Use HTTPS and configure TLS (Transport Layer Security) certificates for custom gateway domains
- Restrict direct backend access where necessary so clients cannot bypass gateway security policies

## Policies

- API Management policies are XML statements applied to API requests and responses, not Azure Policy resource governance rules
- Policy sections:
    - `inbound`: processes the request before it is sent to the backend
    - `backend`: controls forwarding to the backend service
    - `outbound`: processes the response before it is returned to the client
    - `on-error`: handles errors raised during policy processing
- Policies can be scoped globally, to a workspace, product, API, or individual operation
- The `<base />` element inherits policies from the parent scope, its position controls execution order
- Product policies do not apply to requests using API-scoped or all-APIs subscription keys
- Common policy uses:
    - Validate tokens and filter source IP addresses
    - Rate limit short-term request bursts, rejected requests normally receive HTTP 429
    - Enforce usage quotas over longer periods
    - Rewrite URLs and set request or response headers
    - Transform request or response bodies
    - Cache responses to reduce backend load
    - Configure CORS (Cross-Origin Resource Sharing) for browser clients
- Policy and caching support vary by gateway type and service tier

## Service Tiers

- Classic tiers include Developer, Basic, Standard, and Premium
- Developer: intended for development and testing, not production, with no SLA (Service Level Agreement)
- Basic and Standard: dedicated production tiers with different capacity and feature sets
- Premium classic: supports capabilities such as multi-region deployment and virtual network injection
- V2 tiers include Basic v2, Standard v2, and Premium v2, offering updated infrastructure and faster provisioning
- Consumption: serverless gateway that scales with demand and bills by usage
- Networking, availability zones, scaling, and gateway options differ by tier, classic and v2 tiers are not feature-equivalent

## Networking

- A public gateway endpoint does not require the backend API to be publicly accessible
- VNet (Virtual Network) injection in classic Developer and Premium tiers supports:
    - External mode: public API Management endpoints with access to private backends
    - Internal mode: private API Management endpoints with access through private connectivity
- Premium v2 supports VNet injection for private gateway access and connectivity to private backends
- Standard v2 and Premium v2 support outbound VNet integration to reach private backends
- Outbound VNet integration alone does not make the gateway endpoint private
- Inbound private endpoint: provides private client access to the managed gateway in supported tiers
- A private endpoint does not provide outbound connectivity from the gateway to private backends
- Configure private DNS resolution and disable public network access when private-only gateway access is required
- NSGs (Network Security Groups), route tables, and firewalls must allow required service dependencies and backend connections

## Monitoring and Troubleshooting

- Azure Monitor provides metrics, diagnostic logs, and alerts for API Management
- Application Insights can collect request telemetry, backend dependencies, and traces when configured
- Monitor request counts, response codes, gateway duration, and backend duration
- HTTP 401 or 403: check subscription keys, token validation, policy restrictions, and backend authorization
- HTTP 429: check rate-limit policies and whether throttling originates at the gateway or backend
- Backend connection failures: check the backend URL, DNS resolution, routes, NSGs, firewall rules, and certificates
- High latency: compare gateway and backend durations to distinguish policy overhead from a slow backend
- Use request tracing to identify the policy or backend call that failed
- Avoid logging subscription keys, authorization headers, or sensitive request and response bodies