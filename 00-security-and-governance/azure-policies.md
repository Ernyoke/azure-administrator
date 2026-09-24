# Azure Policies

- They enforce organizational standards for compliance
- Policies govern resource properties and configuration, not who can perform an action; access is controlled by Azure RBAC
- Depending on the effect, a policy can only audit compliance or actively block non-compliant requests, such as with Deny
- Azure has "built-in" policies you can use right away
- Policy Definitions: a policy definition is a JSON file used to describe business rules that resources must comply with
- Policy Assignment: the scope a policy applies to, such as a management group, subscription, resource group or resource
- Policy Parameters: values we can pass into your Policy definition so our Policies are more flexible for re-use
- Initiative Definitions: an initiative definition is a collection of policy definitions, that we can assign. eg. A group of policies to enforce PCI-DSS compliance
- Once a policy is assigned it will evaluate for the compliance state periodically
- We can see how compliant we are on the Compliance tab

## Evaluation Timing

- A new or updated assignment is applied to its scope in about 30 minutes
- Standard compliance evaluation runs every 24 hours
- Create and update requests are evaluated when they are made
- Trigger an on-demand evaluation with `az policy state trigger-scan` or `Start-AzPolicyComplianceScan`

## Anatomy of an Azure Policy Definition File

- Display name: identifies the policy and has a 128-character limit
- Policy type: read-only property that identifies who maintains the policy:
    - Built-in: maintained by Microsoft
    - Custom: created by a customer
    - Static: Microsoft-owned policy used for regulatory compliance
- Description: provides context for the policy
- Metadata: optional key-value information stored with the policy
- Mode: determines which resource types are evaluated and whether evaluation uses Azure Resource Manager or a resource provider
- Azure Resource Manager modes:
    - All: evaluates resource groups, subscriptions and all resource types
    - Indexed: evaluates only resource types that support tags and location
- Resource provider modes:
    - Microsoft.ContainerService.Data: deprecated
    - Microsoft.Kubernetes.Data
    - Microsoft.KeyVault.Data
- Parameters: values passed into a policy definition to make it reusable and flexible
    - Name: parameter name
    - Type: string, array, object, boolean, integer, float or datetime
    - Metadata: friendly information displayed by Azure
    - Description: explanation of the parameter
    - Display name: friendly parameter name
    - Strong type: optional multi-select list
    - Assign permissions: allows Azure to create role assignments during policy assignment
    - Default value: optional value used when none is supplied
    - Allowed values: optional list of accepted values
    - Reference parameters with the `parameters()` function and use operators such as `field` and `in` in policy conditions
- Policy rule: consists of `if` and `then` blocks
    - The `if` block defines one or more conditions that determine when the policy is enforced
    - Logical operators combine conditions to define the policy scenario precisely

## Policy Effects

- Deny: fails a resource creation or update request that violates the policy
- Audit: creates a warning event in the activity log for a non-compliant resource without stopping the request
- Append: adds fields to a requested resource during creation or update, such as cost center tags or allowed storage IP addresses
- AuditIfNotExists: audits a resource when a related resource or property does not exist
- DeployIfNotExists: deploys a resource when a specified condition is met, such as configuring encryption after a database is created
- Disabled: ignores the policy rule and is often used for testing
- Modify: adds, updates or removes properties or tags on a resource during creation or update
- DenyAction: blocks requests for specific actions, such as deleting a resource
- Manual: records compliance that is attested manually rather than evaluated automatically
- Effect evaluation order when several policies apply:
    - Disabled
    - Append and Modify
    - Deny
    - Audit
    - AuditIfNotExists and DeployIfNotExists run after the resource provider returns a successful response

## Assignment and Remediation

- Assign a policy or initiative to a management group, subscription, resource group or resource, not to a user
- Assignments apply to child scopes unless excluded or exempted
- Policy enforcement depends on the effect: Audit observes, while Deny blocks non-compliant create and update requests
- Existing non-compliant resources are not deleted by a Deny assignment
- Modify and DeployIfNotExists assignments need a managed identity with the permissions required for remediation
- A remediation task applies Modify or DeployIfNotExists to existing non-compliant resources
- Assigning a remediation policy does not automatically repair all existing resources
- Exclusion: removes a scope from evaluation for that assignment
- Exemption: records a waiver or mitigation for an in-scope resource and can have an expiration date
- `DoNotEnforce`: evaluates compliance without enforcing the policy effect
- Definition location limits assignment scope: a definition must be available at the assignment scope or a parent scope