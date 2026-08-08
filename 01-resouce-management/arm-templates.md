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
    - Other properties: resource-specific settings used to configure the resource
- Reference: the resource declaration identifies the resource type, API version, name, location and configuration

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
    - Object: `contains`, `empty`, `intersection`, `json`, `length` and `union`
    - Resource: `extensionResourceId`, `listAccountSas`, `listKeys`, `listSecrets`, `list*`, `pickZones`, `providers`, `reference`, `resourceId`, `subscriptionResourceId` and `tenantResourceId`
    - Scope: `resourceGroup` and `subscription`
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
    - Cannot use the `reference` function
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

