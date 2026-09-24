# Bicep

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

## Bicep - Template Structure

- Bicep templates use the `.bicep` file extension
- Common declarations:
    - `targetScope`: deployment scope, defaults to `resourceGroup`
    - `param`: input value supplied at deployment time
    - `var`: reusable value or expression calculated within the template
    - `resource`: Azure resource to deploy or reference
    - `module`: reusable Bicep file or ARM JSON template deployed from another Bicep file
    - `output`: value returned after deployment
- Strings use single quotes, such as `'eastus'`
- String interpolation inserts expressions into strings, such as `'st${uniqueString(resourceGroup().id)}'`
- Objects and arrays do not require commas between items on separate lines
- Reference parameters, variables and resources directly by their symbolic names instead of ARM JSON functions such as `parameters()` and `variables()`

## Bicep - Parameters and Variables

- Parameter declaration: `param location string = resourceGroup().location`
- Common parameter types: `string`, `int`, `bool`, `array` and `object`
- A parameter without a default value must be supplied at deployment time
- Decorators appear before a declaration and add metadata or constraints:
    - `@description('Azure region')`: documents the parameter
    - `@allowed(['Standard_LRS', 'Standard_GRS'])`: restricts permitted values
    - `@minLength()` and `@maxLength()`: constrain string or array length
    - `@minValue()` and `@maxValue()`: constrain integer values
    - `@secure()`: marks a string or object parameter as sensitive so its value is not logged in deployment history
- Use `@secure()` with `string` or `object` instead of the ARM JSON types `secureString` and `secureObject`
- Variable declaration: `var storageName = 'st${uniqueString(resourceGroup().id)}'`
- Variable types are inferred from their assigned values
- Output declaration: `output storageId string = storageAccount.id`
- Keep secrets out of ordinary outputs because their values are stored in deployment history

## Bicep - Resources and Dependencies

- Resource declaration format: `resource <symbolicName> '<resourceProvider>/<resourceType>@<apiVersion>' = { ... }`
- Symbolic name: identifier used within the Bicep file, separate from the resource's Azure `name` property
- Resource properties include `name`, `location`, `sku`, `tags` and resource-specific `properties`
- Access resource values through the symbolic name, such as `storageAccount.id` or `storageAccount.properties.primaryEndpoints.blob`
- Referencing another resource's symbolic name creates an implicit deployment dependency
- Use `dependsOn` only when a required dependency cannot be inferred from references
- Resources without dependencies deploy in parallel
- `existing`: references a resource without redeploying it, such as `resource storageAccount 'Microsoft.Storage/storageAccounts@2023-05-01' existing = { name: storageName }`
- An existing resource can specify a different `scope`, such as `resourceGroup('shared-rg')`
- Use `parent` to associate a separately declared child resource with its parent; the child's `name` then contains only the child segment

## Bicep - Modules

- Modules split infrastructure into reusable files with their own parameters, resources and outputs
- Module declaration format: `module <symbolicName> '<path>' = { ... }`
- Module properties:
    - `name`: name of the nested deployment, not the module's file name
    - `params`: values passed to the module's parameters
    - `scope`: target scope when deploying outside the current scope
    - `dependsOn`: explicit dependencies when needed
- Reference a local module with a relative path, such as `'./storage.bicep'`
- Reuse published modules from a private Azure container registry or the public Azure Verified Modules catalog
- Read module outputs with `<moduleSymbolicName>.outputs.<outputName>`
- Referencing a module output creates an implicit dependency on that module
- Modules compile into nested ARM deployments, so local module files do not need separate public hosting

## Bicep - Conditions and Loops

- Conditional deployment: place `if (<condition>)` before a resource or module body
- A condition evaluates to `true` to deploy the resource or module and `false` to skip it
- A parent resource's condition does not automatically apply to separately declared child resources
- Use the ternary operator `<condition> ? <trueValue> : <falseValue>` to select values
- Loop syntax: `[for item in collection: { ... }]`
- Use `range(0, count)` for a fixed number of iterations
- Use `[for (item, index) in collection: { ... }]` when both the item and its zero-based index are needed
- Loops can create multiple resources or modules and generate arrays for properties, variables or outputs
- Resource and module loop instances need unique names
- `@batchSize(1)`: deploys resource or module loop instances sequentially instead of in parallel

## Bicep - Deployment Scopes

- Supported `targetScope` values: `'resourceGroup'`, `'subscription'`, `'managementGroup'` and `'tenant'`
- Set `targetScope = 'subscription'` to deploy subscription-level resources such as resource groups
- Match the deployment command to the template scope, such as `az deployment sub create` for subscription deployments
- Use a module's `scope` property to deploy resources at another scope, such as `scope: resourceGroup('target-rg')`
- The module's `targetScope` must match the scope where it is deployed
- Subscription, management group and tenant deployments require a location for deployment metadata
- The deploying identity needs appropriate permissions at every target scope

## Bicep - Parameter Files

- Supply parameter values through JSON parameter files, Bicep parameter files or command-line arguments
- Bicep parameter files use the `.bicepparam` extension
- `using './main.bicep'`: associates a parameter file with its template
- Assign values without declaring types, such as `param location = 'eastus'`
- Use separate parameter files for environments such as development and production
- Command-line parameter values override values supplied in a parameter file
- `az bicep build-params --file main.bicepparam`: compiles a Bicep parameter file into a JSON parameter file
- Secure parameter decorators do not encrypt values stored in parameter files; do not commit secrets to source control

## Bicep - Authoring and Deployment

- Install the Bicep extension in Visual Studio Code for IntelliSense, validation, linting and resource visualization
- Azure CLI commands:
    - `az bicep install`: installs the Bicep CLI
    - `az bicep upgrade`: upgrades the Bicep CLI
    - `az bicep build --file main.bicep`: compiles the template into ARM JSON without deploying resources
    - `az bicep decompile --file azuredeploy.json`: converts ARM JSON to Bicep; manual corrections can be required
    - `az deployment group validate --resource-group <rg> --template-file main.bicep`: validates a deployment
    - `az deployment group what-if --resource-group <rg> --template-file main.bicep`: previews resource changes
    - `az deployment group create --resource-group <rg> --template-file main.bicep`: deploys at resource group scope
    - `az deployment group create --resource-group <rg> --parameters main.bicepparam`: deploys the template referenced by the parameter file's `using` declaration
- Azure PowerShell: `New-AzResourceGroupDeployment -ResourceGroupName <rg> -TemplateFile main.bicep`
- Azure PowerShell requires the Bicep CLI to be installed and available on `PATH`
- Supply required parameters when validating, previewing or deploying a template
- Bicep uses ARM deployment modes; removing a resource declaration does not delete the resource during an incremental deployment