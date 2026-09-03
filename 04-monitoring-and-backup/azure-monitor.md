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
        - We must create a diagnostic setting to specify a destination for them to collected for each resource
    - Platform metrics will write to the Azure Monitor metrics database with no configuration
    - Access platform metrics from Metrics Explorer
    - Trending and other analysis using Log Analytics
    - We can:
        - Copy platform metrics to Logs
        - Send resource logs to Azure Storage for archiving
        - Stream metrics to other locations using Event Hubs
- Azure Subscriptions:
    - Telemetry related to the health and operation of our Azure subscriptuion
    - Azure Service Health: provides information about the health of Azure services in our subscription that our applications and resources rely on
- Tenants:
    - Telemetry related to your Azure tenant is collected from tenant-wide services such as Azure Active Directory
    - Azure Active Directory reporting contains the history of sign-in activity and audit trail of changes made within a particular tenant
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

## Azure Monitor Metrics

- Collects numeric data from monitored resources into a time-series database
- Metrics are numerical values collected at regular intervals that describe an aspect of a system at a particular time
- Metrics are lightweight and support near real-time scenarios, making them useful for alerting and fast issue detection
- Metrics Explorer provides interactive metrics analysis

## Log Analytics

- Log Analytics is a tool in the Azure portal used to edit and run log queries with data in Azure Monitor Logs
- Log Analytics uses a query language called KQL

## Log Analytics Workspaces

- A Log Analytics workspace is a unique environment for Azure Monitor log data
- Each workspace has its own data repository and configuration
- Data sources and solutions are configured to store their data in a particular workspace

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

- Control commands can modify data and metadata and has its own syntax different from KQL
- The following control command creates a new Kusto table with two columns

```
.create table Logs (Level:string, Text:string)
```

- A very common control command is `.show` for example, this will count all tables:

```
.show table
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