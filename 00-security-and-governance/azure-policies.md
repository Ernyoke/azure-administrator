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