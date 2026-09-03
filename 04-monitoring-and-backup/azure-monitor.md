# Azure Monitor

- It is a unified observability service for collecting, analyzing, and acting on telemetry from cloud and hybrid environments

## Key Concepts

- Azure Monitor collects and analyzes monitoring data from a variety of sources and provides tools to take action
- Two fundamental data types are:
    - Metrics: numeric values that describe performance
    - Logs: detailed records of events
- Functions of Azure Monitor:
    - Insights:
        - Provides tailored visualizations and metrics for specific Azure services such as VMs, containers, and applications
    - Visualize:
        - Create dashboards, Power BI reports, and workbooks to understand data trends
    - Analyze:
        - Use Log Analytics and Metrics Explorer to derive actionable insights
    - Respond:
        - Set up alerts and autoscale policies
    - Integrate:
        - Connect with Logic Apps or export data via APIs for advanced workflows

## Azure Monitor - Sources

- Application code:
    - Performance and functionality of application code: performance traces, application logs, and user telemetry
        - Application code must be instrumented to collect telemetry for Application Insights
    - Availability tests: test the responsiveness of the application from different locations on the public internet
    - Metrics: describe the performance and operation of your application, including custom metrics
    - Logs: contain operational data about your application, including page views, application requests, exceptions, and traces
    - Application data such as availability test results can be exported to Azure Storage through diagnostic settings for archiving
- Operating systems:
    - Legacy agent - Log Analytics Agent (MMA):
        - Retired on August 31, 2024 and no longer supported
        - Historically used to:
            - Collect guest OS logs
            - Send data to Log Analytics
    - Current supported agent - Azure Monitor Agent (AMA):
        - Replaces Log Analytics Agent
        - Uses Data Collection Rules (DCRs)
        - Works with Azure VMs and Azure Arc-enabled servers
        - Collects guest OS logs and performance data
    - Diagnostics and data collection:
        - Azure Diagnostics extensions: retired on March 31, 2026 and no longer supported
        - Modern approach: Azure Monitor Agent + Data Collection Rules
        - Diagnostic settings destinations:
            - Log Analytics workspaces
            - Azure Storage accounts
            - Azure Event Hubs
    - Metrics:
        - Near real-time numeric time-series
        - Can be viewed in Metrics Explorer
    - Logs:
        - Stored in Log Analytics workspaces
        - Can be queried with KQL (Kusto Query Language)
    - Application Insights:
        - Part of Azure Monitor
        - Stores data in a Log Analytics workspace
        - Collects:
            - Application telemetry
            - Performance
            - Failures
            - Dependencies
- Azure Resources:
    - Resource Logs:
        - Resource logs provide insights into the internal operation of an Azure resource
        - Resource logs are created automatically
        - We must create a diagnostic setting on each resource to specify a destination for them to be collected
    - Platform metrics will write to the Azure Monitor metrics database with no configuration
    - Access platform metrics from Metrics Explorer
    - Trending and other analysis using Log Analytics
    - We can:
        - Copy platform metrics to Logs
        - Send resource logs to Azure Storage for archiving
        - Stream metrics to other locations using Event Hubs
- Azure Subscriptions:
    - Telemetry related to the health and operation of our Azure subscription
    - Azure Activity log: subscription-level log of control plane operations, service health events and autoscale events
    - Azure Service Health: provides information about the health of Azure services in our subscription that our applications and resources rely on
- Tenants:
    - Telemetry related to our Azure tenant is collected from tenant-wide services such as Microsoft Entra ID
    - Microsoft Entra ID reporting contains the history of sign-in activity and an audit trail of changes made within a particular tenant
- Custom sources:
    - We may need to monitor other resources that have telemetry that can't be collected with the other data sources
    - For these resources, we can write this data to either Metrics or Logs using an Azure Monitor API
    - We can collect log data from any REST client and store in Log Analytics and Azure Monitor metrics database

## Azure Monitor Logs

- Collects and organizes log and performance data from monitored resources
- Consolidates data from different sources into Log Analytics workspaces, including:
    - Platform logs from Azure services
    - Log and performance data from virtual machine agents
    - Usage and performance data from applications
- Data from these sources can be analyzed together using a sophisticated query language capable of analyzing millions of records
- Log Analytics provides interactive access to log queries and their results
- Logs are stored as sets of records in tables, each table having its own schema
- Logs are not suited to near real-time alerting because of ingestion latency, typically a few minutes

## Azure Monitor Metrics

- Collects numeric data from monitored resources into a time-series database
- Metrics are numerical values collected at regular intervals that describe an aspect of a system at a particular time
- Metrics are lightweight and support near real-time scenarios, making them useful for alerting and fast issue detection
- Metrics Explorer provides interactive metrics analysis
- Platform metrics are collected automatically for Azure resources with no configuration required
- Platform metrics are retained for 93 days
- Metrics are stored at one-minute granularity by default and are aggregated over the selected time grain
- Aggregation types are: average, minimum, maximum, sum and count
- Dimensions are name-value pairs that allow filtering and splitting a metric, such as splitting network traffic by port
- Guest OS metrics such as memory and disk space require an agent, they are not collected by the host platform
- Metric alerts evaluate metrics without needing a Log Analytics workspace

## Metrics Explorer

- Tool in the Azure portal used to plot charts from platform and custom metrics
- Steps to create a chart:
    - Select a scope, one or more resources of the same type
    - Select a metric namespace, such as the guest OS namespace for VMs
    - Select a metric and its aggregation
    - Optionally apply filters and splitting on dimensions
- Charts can be pinned to an Azure dashboard or shared as a workbook
- A new alert rule can be created directly from a chart

## Log Analytics

- Log Analytics is a tool in the Azure portal used to edit and run log queries with data in Azure Monitor Logs
- Log Analytics uses a query language called KQL
- Queries can be saved, pinned to dashboards, exported to Power BI or Excel, and used to create log alert rules
- The scope of a query determines the data searched, either a workspace or a specific resource

## Log Analytics Workspaces

- A Log Analytics workspace is a unique environment for Azure Monitor log data
- Each workspace has its own data repository and configuration
- Data sources and solutions are configured to store their data in a particular workspace
- A workspace is an Azure resource in a resource group and a region, data is stored in the workspace region
- A single subscription can contain multiple workspaces, and a single workspace can receive data from resources in other subscriptions and regions
- Reasons to use multiple workspaces:
    - Data sovereignty requirements to keep data in a specific region
    - Splitting billing between departments or subsidiaries
    - Isolating access between separate teams
- Default retention is 30 days, or 90 days when Microsoft Sentinel or Application Insights is enabled
- Interactive retention can be increased up to 730 days, and long-term retention up to 12 years
- Retention can be configured per workspace and overridden per table
- Pricing models:
    - Pay-as-you-go: billed per GB ingested
    - Commitment tiers: fixed daily volume at a discount, starting at 100 GB per day
- A daily cap can be set to limit ingestion and control cost, data is dropped once the cap is reached
- Access control modes:
    - Require workspace permissions: only workspace-level permissions grant access, also called workspace-context
    - Use resource or workspace permissions: users with read access to a resource can see its data, also called resource-context
- Built-in roles:
    - Log Analytics Reader: view and search all monitoring data and view monitoring settings
    - Log Analytics Contributor: includes Reader plus the ability to create and configure workspaces, agents and data collection rules
    - Monitoring Reader: read all monitoring data across Azure Monitor
    - Monitoring Contributor: read all monitoring data and edit monitoring settings

## Diagnostic Settings

- A diagnostic setting defines where platform logs and platform metrics for a resource are sent
- Platform logs and metrics are not stored long term unless a diagnostic setting is created
- Destinations:
    - Log Analytics workspace: analysis with KQL, alerts and correlation with other data
    - Azure Storage account: cheap long-term archiving and audit retention
    - Azure Event Hubs: streaming to third-party SIEM or external tools
    - Azure Monitor partner solutions
- Each resource supports up to five diagnostic settings
- The Activity log has its own diagnostic setting at the subscription level to export it to a workspace, storage or event hub
- Diagnostic settings can be deployed at scale using Azure Policy with `DeployIfNotExists` effects

## Activity Log

- Subscription-level log that records control plane operations performed on resources
- Answers what happened, who performed the operation, and when
- Retained for 90 days by default, and free of charge
- To keep entries longer than 90 days, export them with a diagnostic setting
- Categories:
    - Administrative: create, update, delete and action operations sent through Azure Resource Manager, including RBAC changes
    - Service Health: incidents, planned maintenance and health advisories for Azure services
    - Resource Health: health changes of individual Azure resources
    - Alert: activations of Azure alerts
    - Autoscale: operations of autoscale engine rules
    - Recommendation: recommendations from Azure Advisor
    - Security: alerts generated by Microsoft Defender for Cloud
    - Policy: results of Azure Policy effects such as audit and deny
- The Activity log does not include read (GET) operations or operations performed inside the guest OS
- When exported to a workspace, entries are stored in the `AzureActivity` table

## Azure Monitor Agent and Data Collection Rules

- Azure Monitor Agent (AMA) collects guest OS logs and performance data from Windows and Linux machines
- Supported on Azure VMs, virtual machine scale sets, and on-premises or other cloud servers through Azure Arc
- Installed as a VM extension and can be deployed at scale with Azure Policy
- Requires a system-assigned or user-assigned managed identity on the machine
- Data Collection Rule (DCR): an Azure resource that defines what data to collect, how to transform it, and where to send it
- A single machine can be associated with multiple DCRs, and a DCR can target many machines
- Data Collection Endpoint (DCE): used when machines connect over a private network or Azure Private Link
- AMA can send the same data to multiple workspaces, which the legacy Log Analytics agent could not do reliably

## Insights

- VM insights:
    - Monitors performance and health of VMs and scale sets
    - Uses Azure Monitor Agent and the Dependency agent for the map feature
    - The map feature shows running processes and dependencies on other machines
- Container insights: monitors AKS and container workloads, collecting performance and container logs
- Storage insights, Network insights and Application Insights provide equivalent service-specific views
- Application Insights:
    - Application performance monitoring (APM) for web applications
    - Collects requests, dependencies, exceptions, traces, page views and custom events
    - Workspace-based Application Insights stores its data in a Log Analytics workspace
    - Availability tests check responsiveness of an endpoint from multiple global locations
    - The Application Map shows components and their dependencies with failure and latency data

## Visualizations

- Workbooks: interactive reports combining text, log queries, metrics and parameters
- Azure dashboards: pin charts, query results and other tiles into a single shared view in the portal
- Power BI: business analytics over exported Azure Monitor data
- Grafana: supported through the Azure Monitor data source and Azure Managed Grafana

## Alerts

- Alerts proactively notify us when an important condition is found in monitoring data
- Components of an alert rule:
    - Scope: the target resource being monitored
    - Condition: the signal and the logic that determines if the alert fires
    - Actions: the action group invoked when the alert fires
    - Details: name, description and severity
- Severity levels:
    - Sev 0: critical
    - Sev 1: error
    - Sev 2: warning
    - Sev 3: informational
    - Sev 4: verbose
- Alert rule types:
    - Metric alerts:
        - Evaluate platform, custom or guest OS metrics at regular intervals
        - Stateful, they automatically resolve when the condition clears
        - Support static thresholds and dynamic thresholds based on machine learning of historical behaviour
        - Support multi-resource rules across VMs in the same region and subscription
    - Log alerts:
        - Run a saved KQL query on a schedule against a workspace or resource
        - Types are number of results and metric measurement
        - Higher latency than metric alerts because they depend on log ingestion
    - Activity log alerts:
        - Fire on new Activity log events, such as a VM being deleted
        - Service Health alerts are a special type of Activity log alert
    - Resource Health alerts: fire on health transitions of a specific resource
    - Smart detection alerts: automatic detection of anomalies in Application Insights
- Alert states are New, Acknowledged and Closed, and are independent of the underlying condition state
- Alert processing rules can suppress notifications during planned maintenance windows or add action groups at scale

## Action Groups

- An action group is a reusable collection of notification preferences and actions triggered by an alert
- Action groups are a global resource and can be shared by many alert rules
- Notification types:
    - Email, including email to an Azure Resource Manager role such as Owner or Contributor
    - SMS
    - Push notification to the Azure mobile app
    - Voice call
- Action types:
    - Automation runbook
    - Azure Function
    - Webhook and secure webhook
    - Logic App
    - ITSM connector
    - Event hub
- Rate limiting applies to notifications, for example no more than one SMS every five minutes and one email every hour per action group

## Autoscale

- Azure Monitor autoscale adds or removes instances based on metrics or a schedule
- Applies to virtual machine scale sets, App Service plans, Azure Spring Apps and Cloud Services
- An autoscale setting contains profiles, and each profile contains rules and instance limits of minimum, maximum and default
- Rule elements are a metric source, a time grain and aggregation, a threshold, an operator, an action and a cool-down period
- The cool-down period prevents rapid repeated scaling, known as flapping
- Scale-out rules are evaluated with OR logic and scale-in rules with AND logic when multiple rules exist
- Autoscale operations are recorded in the Activity log

## Common Log Analytics Tables

- `Heartbeat`: agent connectivity records, used to confirm an agent is reporting
- `Perf`: performance counters collected from Windows and Linux machines
- `Event`: Windows event log records
- `Syslog`: Linux syslog records
- `AzureActivity`: exported Azure Activity log entries
- `AzureDiagnostics` and resource-specific tables: resource logs sent by diagnostic settings
- `InsightsMetrics`: performance data collected by VM insights and Container insights
- `AzureMetrics`: platform metrics routed to a workspace
- `SigninLogs` and `AuditLogs`: Microsoft Entra ID sign-in and audit data

## Kusto

- Azure Monitor Logs is based on Azure Data Explorer, and log queries use KQL (Kusto Query Language)
- KQL can be used in:
    - Log Analytics
    - Log alert rules
    - Workbooks
    - Azure dashboards
    - Logic Apps
    - PowerShell
    - Azure Monitor Logs API
- Kusto is based on relational database management systems and supports entities such as databases, tables and columns
- Query operators include:
    - Calculated columns
    - Searching and filtering rows
    - Group-by aggregations
    - Join functions
- Kusto queries execute in the context of a Kusto database attached to a Kusto cluster

### Kusto Entities

- Kusto is generally composed of clusters, databases, tables, columns and functions
- Cluster: entity that holds databases
- Database: named entity that holds tables and stored functions
- Table: named entity that holds data in an ordered set of columns and zero or more rows, with each row holding one value for each column
- Column: named entity with a scalar data type, referenced relative to the tabular data stream in the context of the operator using it
- Stored function: named entity that enables the reuse of Kusto queries or query parts
- External table: entity that references data stored outside the Kusto database
- External tables support exporting Kusto data to external storage and querying external data without ingesting it into Kusto

### Kusto Scalar Data Types

- Scalar: quantity fully described by a magnitude or numerical value
- Data type: defines how a piece of data is interpreted, such as an integer
- Kusto data types define column values and the values expected by function parameters
- `bool`, `boolean`: true or false value
- `datetime`, `date`: date and time value, always stored in Coordinated Universal Time (UTC), such as `2015-12-31 23:59:59.9`
- `decimal`: 128-bit decimal number, such as `12.88`
- `int`: signed 32-bit integer, such as `5`
- `long`: signed 64-bit integer
- `guid`, `uuid`, `uniqueid`: 128-bit globally unique value, such as `74be27de-1e4e-49d9-b579-fe0b331d3642`
- `real`: 64-bit double-precision floating-point number
- `string`: Unicode string encoded in UTF-8 and limited to 1 MB by default, such as `"hello world"`
- `timespan`: time interval, such as `2d` for 2 days, `30m` for 30 minutes or `1tick` for 100 nanoseconds
- `dynamic`: special data type that can contain:
    - A primitive scalar value such as `bool`, `datetime`, `guid`, `int`, `long`, `real`, `string` or `timespan`
    - An array of values such as `[1, 2, 3, "hello"]`
    - A property bag such as `{"a": 1, "b": {"a": 2}}`
- `null`: special value representing a missing value

### Kusto Control Commands

- Control commands can modify data and metadata and have their own syntax different from KQL
- The following control command creates a new Kusto table with two columns

```
.create table Logs (Level:string, Text:string)
```

- A very common control command is `.show`, for example this will count all tables:

```
.show tables
| count
```

### Kusto Functions

- Functions: reusable queries or query parts
- Stored functions:
    - User-defined functions stored and managed as database schema entities
    - Categories include:
        - Scalar functions: accept scalar data types and return scalar values
        - Tabular functions: accept tabular data and return tabular data
- Query-defined functions: user-defined functions declared and used within the scope of a single query
- Built-in functions: functions defined by Kusto that users cannot modify, including:
    - Special functions: select Kusto entities, such as `cluster()`
    - Aggregation functions: calculate a result from a set of values and return a single value, such as `count()`
    - Window functions: operate on multiple rows in a row set, such as `row_number()`

### Kusto Scalar Operators

- Scalar operators perform comparisons and operations on scalar data types
- Bitwise operators:
    - `binary_and`
    - `binary_not`
    - `binary_or`
    - `binary_shift_left`
    - `binary_shift_right`
    - `binary_xor`
- Logical operators:
    - Equality: `==`
    - Inequality: `!=`
    - Logical AND: `and`
    - Logical OR: `or`
- Datetime and timespan arithmetic:
    - Add or subtract datetime values, such as `datetime(1997-06-25) - datetime(1910-06-11)`
    - Add, subtract, divide or multiply timespan values, such as `1d + 2d`
- Numerical operators for `int`, `long` and `real` values:
    - Arithmetic: add `+`, subtract `-`, multiply `*`, divide `/` and modulo `%`
    - Comparison: less than `<`, greater than `>`, equal `==`, not equal `!=`, less than or equal `<=` and greater than or equal `>=`
    - Membership: equal to one of the elements `in` and not equal to any element `!in`
- String operators include `==`, `!=`, `=~`, `!~`, `has`, `hasprefix`, `hassuffix`, `contains`, `startswith`, `endswith`, `matches regex`, `in` and `has_any`
- `between`: matches input within an inclusive range, such as:
    - `Table1 | where Num1 between (1 .. 10)`
    - `Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-02))`

### Kusto Tabular Operators

- Tabular operators process input tables containing rows and columns
- `count`: returns the number of rows in a table
- `take`: returns up to the specified number of rows
- `sort`: orders rows by one or more columns
- `project`: selects a specific set of columns
- `where`: filters rows to those that satisfy a predicate
- `top`: returns the first specified number of rows sorted by one or more columns
- `extend`: adds calculated columns to a table
- `summarize`: aggregates groups of rows
- `render`: displays query results as graphical output