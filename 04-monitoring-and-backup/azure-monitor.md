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
    