# ARM Templates

## Infrastructure as Code

- IaC (Infrastructure as Code): the process of managing and provisioning infrastructure through machine-readable definition files instead of manual hardware configuration or interactive tools
- IaC uses scripts or definition files, such as JSON, to deploy and configure cloud services
- IaC approaches:
    - Declarative: defines the desired end state and lets the platform determine how to reach it
    - Imperative: defines the commands and steps required to reach the desired state

## ARM Templates

- ARM (Azure Resource Manager) templates are JSON files that define Azure resources to provision and services to configure
- ARM templates are declarative and deploy resources in a consistent, repeatable state
- ARM templates provide:
    - Rapid deployment: create, remove and share complete architectures in minutes
    - Consistency: reduce manual configuration mistakes
    - Compliance: establish a known architecture baseline
    - Modularity: split an architecture across linked or nested templates and reuse components
    - Extensibility: run PowerShell or Bash deployment scripts
    - Testing: validate templates with the ARM Template Toolkit (`arm-ttk`)
    - Preview changes: use the what-if operation to see proposed changes before deployment
    - Built-in validation: validate a template before Azure deploys its resources
    - Tracked deployments: retain deployment history and track architecture changes over time
    - Policy as code: deploy Azure Policy definitions and assignments to enforce compliance
    - CI/CD (Continuous Integration and Continuous Delivery) integration
    - Exportable code: export existing resource groups or resources as ARM templates
    - Authoring tools: use Visual Studio Code features to create and validate templates
- ARM templates can deploy nearly all artifacts previously supported by Azure Blueprints
- An ARM template is stored locally or in source control and has no active relationship with resources after deployment
- Azure Blueprints maintained relationships between blueprint assignments and deployed resources, but the service was retired on July 11, 2026
- ARM template limits:
    - 4 MB maximum template size and 4 MB maximum parameter file size
    - 256 parameters, 256 variables, 800 resources and 64 outputs per template
    - 800 deployments retained in the deployment history of a resource group

## ARM Template - Skeleton

- Skeleton: the general structure of an ARM template
- `$schema`: describes the properties available within the template
- `contentVersion`: specifies the version of the template and can contain any value
- `apiProfile`: avoids specifying an API version for each resource in the template
- `parameters`: values passed to the template
- `variables`: values created by transforming parameters or resource properties with function expressions
- `functions`: user-defined functions available within the template
- `resources`: Azure resources to deploy or update
- `outputs`: values returned after deployment

## ARM Template - Resources

- Resource: an Azure resource to provision
- Resource properties:
    - `type`: resource type in the format `{resourceProvider}/{resourceType}`
    - `apiVersion`: version of the REST API used for the resource; each resource provider publishes its own API versions
    - `name`: name of the resource
    - `location`: Azure region where the resource is deployed; required by most resources
    - `tags`: name and value pairs applied to the resource for organization and billing
    - `sku` and `kind`: service tier and variant of the resource
    - `identity`: system-assigned or user-assigned managed identity of the resource
    - `dependsOn`: resources that must be deployed before this resource
    - `condition`: boolean expression that determines whether the resource is deployed
    - `copy`: creates multiple instances of the resource
    - `properties`: resource-specific settings used to configure the resource
- Child resources are declared either nested inside the parent resource or at the top level with a fully qualified type and name

### Dependencies

- `dependsOn`: explicitly orders deployment by listing the names or resource IDs of prerequisite resources
- Using the `reference` or `list*` functions on another resource creates an implicit dependency
- Resources without dependencies are deployed in parallel, so unnecessary dependencies slow down deployment
- Circular dependencies cause the deployment to fail validation

### Conditional Deployment

- `condition`: deploys a resource only when the expression evaluates to `true`
- Example: `"condition": "[equals(parameters('newOrExisting'), 'new')]"`
- A `false` condition does not delete an existing resource, it only skips deployment
- Use the `if` function in properties and outputs that reference conditionally deployed resources

### Copy Loops

- `copy`: deploys multiple instances of a resource, property, variable or output
- Copy properties:
    - `name`: name of the loop
    - `count`: number of iterations, maximum 800
    - `mode`: `Parallel` (default) or `Serial`
    - `batchSize`: number of instances deployed at a time in serial mode
- `copyIndex()`: returns the zero-based current iteration; `copyIndex(1)` offsets the value by one
- `dependsOn` can reference an entire loop by its copy `name`

## ARM Template - Deployment Scripts

- Deployment script: runs Azure CLI or Azure PowerShell operations during an ARM deployment by using `Microsoft.Resources/deploymentScripts`
- Used for actions that ARM resources cannot express declaratively, such as data-plane operations
- Azure creates an Azure Container Instance (ACI) and storage account to run the script and store its results
- Scripts can be inline or external and can return outputs to the template
- Only user-assigned managed identities are supported for authenticating the script to Azure
- Redeploying an unchanged deployment script does not execute it again
- Change `forceUpdateTag` to force another execution
- `cleanupPreference`: deletes supporting resources `Always`, `OnSuccess` or `OnExpiration`

## ARM Template - Parameters

- Parameters: values passed into an ARM template at deployment time
- Parameter properties:
    - `type`: expected data type of the input value
    - Supported types: `string`, `secureString`, `int`, `bool`, `object`, `secureObject` and `array`
    - `defaultValue`: value used when no value is provided
    - `allowedValues`: array of permitted values
    - `minValue`: minimum permitted value for an integer
    - `maxValue`: maximum permitted value for an integer
    - `minLength`: minimum permitted length of a string or array
    - `maxLength`: maximum permitted length of a string or array
    - `metadata.description`: description displayed to users in the Azure portal
- Use `secureString` and `secureObject` for passwords and secrets, as their values are not logged or returned in the deployment history
- Never place a secret in an `outputs` section, because outputs are stored in the deployment history
- A parameter without a `defaultValue` must be supplied at deployment time

### Parameter Files

- Parameter file: a separate JSON file that supplies parameter values for a deployment
- A parameter file uses its own `$schema`, a `contentVersion` and a `parameters` object where each entry has a `value`
- Naming convention: `azuredeploy.parameters.json` next to `azuredeploy.json`
- A parameter can use `reference` to retrieve a secret from Azure Key Vault instead of a literal `value`
- Values passed on the command line override values in the parameter file

## ARM Template - Functions

- Functions: apply transformations and retrieve values within an ARM template
- Function types:
    - Template functions: built-in functions provided by ARM
    - User-defined functions: custom functions declared in the template
- Functions are called with parentheses, such as `functionName()`
- Template function categories:
    - Array: `array`, `concat`, `contains`, `createArray`, `empty`, `first`, `intersection`, `last`, `length`, `min`, `max`, `range`, `skip`, `take` and `union`
    - Comparison: `coalesce`, `equals`, `less`, `lessOrEquals`, `greater` and `greaterOrEquals`
    - Date: `dateTimeAdd` and `utcNow`
    - Deployment: `deployment`, `environment`, `parameters` and `variables`
    - Logical: `and`, `bool`, `false`, `if`, `not`, `or` and `true`
    - Numeric: `add`, `copyIndex`, `div`, `float`, `int`, `min`, `max`, `mod`, `mul` and `sub`
    - Object: `contains`, `createObject`, `empty`, `intersection`, `items`, `json`, `length`, `null` and `union`
    - Resource: `extensionResourceId`, `listAccountSas`, `listKeys`, `listSecrets`, `list*`, `pickZones`, `providers`, `reference`, `resourceId`, `subscriptionResourceId` and `tenantResourceId`
    - Scope: `resourceGroup`, `subscription`, `managementGroup` and `tenant`
    - String: `base64`, `base64ToJson`, `base64ToString`, `concat`, `contains`, `dataUri`, `dataUriToString`, `empty`, `endsWith`, `first`, `format`, `guid`, `indexOf`, `last`, `lastIndexOf`, `length`, `newGuid`, `padLeft`, `replace`, `skip`, `split`, `startsWith`, `string`, `substring`, `take`, `toLower`, `toUpper`, `trim`, `uniqueString`, `uri`, `uriComponent` and `uriComponentToString`

### User Defined Functions

- User-defined function: a reusable named expression that avoids repeating complex logic throughout a template
- A user-defined function contains:
    - `namespace`: groups custom functions and prevents naming conflicts with built-in functions
    - `members`: contains one or more named function definitions
    - `parameters`: typed input values accepted by the function
    - `output`: typed value and expression returned by the function
- Call a user-defined function with its namespace and member name, such as `contoso.buildName()`
- User-defined function restrictions:
    - Cannot access template variables
    - Can access only parameters declared within the function
    - Cannot call another user-defined function
    - Cannot use the `reference` function or any of the `list*` functions
    - Cannot define default values for function parameters
- User-defined function example:

    ```json
    {
        "functions": [
            {
                "namespace": "contoso",
                "members": {
                    "buildName": {
                        "parameters": [
                            {
                                "name": "prefix",
                                "type": "string"
                            },
                            {
                                "name": "suffix",
                                "type": "string"
                            }
                        ],
                        "output": {
                            "type": "string",
                            "value": "[toLower(concat(parameters('prefix'), '-', parameters('suffix')))]"
                        }
                    }
                }
            }
        ],
        "outputs": {
            "resourceName": {
                "type": "string",
                "value": "[contoso.buildName('app', 'prod')]"
            }
        }
    }
    ```

## ARM Template - Variables

### Basic Variables

- Variables: expressions or values defined in the `variables` section and reused throughout a template
- Commonly used for dynamically generated names or combined property values
- Variable definition example:

    ```json
    "variables": {
        "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
    }
    ```

- Reference a variable with `[variables('variableName')]`
- Resource reference example:

    ```json
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]"
        }
    ]
    ```

### Nested Variables

- Nested variables group related settings, such as configuration values for test and production environments
- Nested variable example:

    ```json
    "variables": {
        "environmentSettings": {
            "test": {
                "instanceSize": "Small",
                "instanceCount": 1
            },
            "prod": {
                "instanceSize": "Large",
                "instanceCount": 4
            }
        }
    }
    ```

- A parameter can select the environment:

    ```json
    "parameters": {
        "environmentName": {
            "type": "string",
            "allowedValues": [
                "test",
                "prod"
            ]
        }
    }
    ```

- Reference an environment-specific nested value with `[variables('environmentSettings')[parameters('environmentName')].instanceSize]`

### Key Takeaways

- Use variables to simplify templates and reuse values
- Use nested variables to manage environment-specific settings
- Use the `variables()` function to retrieve variable values
- Combine variables with parameters to make templates dynamic

## ARM Template - Outputs

- Outputs: values returned from deployed resources for programmatic use
- Each output specifies a `type` and `value`
- Retrieve deployment outputs through the Azure API by using Azure CLI, Azure PowerShell or an SDK
- Outputs are stored in the deployment history, so they must never contain secrets

## ARM Template - Deployment Scopes

- Deployment scope: the level at which a template is deployed
- Supported scopes:
    - Resource group: the most common scope, used for deploying resources
    - Subscription: used for resource groups, policy assignments and role assignments
    - Management group: used for policy and role definitions across subscriptions
    - Tenant: used for management groups and tenant-wide settings
- The `$schema` value differs per scope, for example `deploymentTemplate.json` for resource groups and `subscriptionDeploymentTemplate.json` for subscriptions
- Subscription, management group and tenant deployments require a `location` for the deployment metadata
- A template can target resources in another scope with a nested `Microsoft.Resources/deployments` resource that sets `subscriptionId` and `resourceGroup`

## ARM Template - Deployment Modes

- Incremental (default): resources in the template are created or updated and resources in the resource group that are not in the template are left unchanged
- Complete: resources in the resource group that are not in the template are deleted
- Complete mode is only available for resource group deployments; other scopes support incremental mode only
- In both modes, ARM re-creates the declaration of any resource included in the template, so properties omitted from the template are reset to their defaults
- Use the what-if operation before a complete mode deployment to see which resources would be deleted

## ARM Template - Deployment

- Deployment options: Azure portal, Azure CLI, Azure PowerShell, REST API, Cloud Shell and CI/CD pipelines
- Azure CLI:
    - `az deployment group create --resource-group <rg> --template-file azuredeploy.json --parameters azuredeploy.parameters.json`
    - `az deployment group what-if` previews changes
    - `az deployment group validate` validates the template without deploying
    - `az deployment sub create --location <region> --template-file <file>` deploys at subscription scope
- Azure PowerShell:
    - `New-AzResourceGroupDeployment -ResourceGroupName <rg> -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json`
    - `-Mode Complete` switches from the default incremental mode
    - `Test-AzResourceGroupDeployment` validates the template
    - `New-AzSubscriptionDeployment -Location <region>` deploys at subscription scope
- Azure portal:
    - Deploy a custom template: paste, upload or select a template from the Quickstart gallery
    - Custom deployment builds a parameter form from the `parameters` section of the template
- Deployments are named, and the deployment history of a resource group is viewed under Settings > Deployments
- Redeploying a previous successful deployment from the history rolls a resource group back to an earlier state

## ARM Template - Exporting Templates

- Export template generates a template from resources that already exist
- Export options:
    - Resource group: exports all or selected resources in the group
    - Individual resource: exports a single resource
    - Deployment history: exports the exact template that was used for a past deployment
- Exported templates from a resource group often need editing before they can be redeployed
- `az group export --name <rg>` and `Export-AzResourceGroup -ResourceGroupName <rg>` export from the command line
- Azure Quickstart Templates is a community gallery of ready-made templates

## ARM Template - Linked and Nested Templates

- Modular templates split a large deployment into smaller, reusable templates
- Nested template: the child template is embedded inside the `template` property of a `Microsoft.Resources/deployments` resource
- Linked template: the child template is referenced through `templateLink.uri`
- A linked template must be reachable by a URI, so it cannot be a local file and is usually stored in Azure Blob Storage or a repository
- Secure a linked template in storage with a SAS (shared access signature) token
- `expressionEvaluationOptions.scope`:
    - `outer` (default): expressions are evaluated in the scope of the parent template
    - `inner`: expressions are evaluated in the scope of the nested template
- Each linked or nested template creates its own deployment entry in the deployment history

## Bicep

- Bicep is a domain-specific language (DSL) that provides a simpler syntax for authoring ARM deployments
- Bicep files are transpiled into ARM JSON templates and use the same deployment engine, validation and what-if operations
- Bicep advantages:
    - Concise syntax without JSON expressions and escaped brackets
    - Automatic dependency management when one resource references another
    - Modules replace linked and nested templates
    - Type safety and IntelliSense in Visual Studio Code
    - No state file to manage, because Azure holds the state of the resources
- `az bicep build` converts a Bicep file to JSON and `az bicep decompile` converts a JSON template to Bicep
- `az deployment group create --template-file main.bicep` deploys a Bicep file directly
