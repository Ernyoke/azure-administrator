# Azure Policies

- They enforce organizational standards for compliance
- Policies do not restrict access, they only observe for compliance
- Azure has "built-in" policies you can use right away
- Policy Definitions: a policy definition is a JSON file used to describe business rules to control access to resources
- Policy Assignment: the scope of a policy can affect. Assigned to a user, a resource group, or a management group
- Policy Parameters: values we can pass into your Policy definition so our Policies are more flexible for re-use
- Initiative Definitions: an initiative definition is a collection of policy definitions, that we can assign. eg. A group of policies to enforce PCI-DSS compliance
 -Once a policy is assigned it will evaluate for the compliance state periodically
- We can see how compliant we are on the Compliance tab

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