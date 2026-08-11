# Azure Site Recovery

- Azure Site Recovery (ASR) is a hybrid (on-premise to cloud) backup solution for site-to-site recovery
- ASR is one of the tools useful for your business continuity and disaster recovery (BCDR) strategy
- Site Recovery replicates workloads from a primary site to a secondary site
- In case primary site suffers a failure, Site Recovery will fail-over to the secondary site to ensure continuity of services
- Site Recover can replicate:
    - Azure VMs between regions (cross-region replication)
    - Windows, Any OS, and Linux
    - On-premise to Azure
    - Between other Cloud Service Providers eg AWS to Azure
    - VMWare, Hyper-V, or Physical Machines

## RPO vs RTO

- Recovery Point Objectives (RPO) how often you backup (how much data loss can you tolerate?)
- Recovery Time Objectives (RTO) how quickly you can recover a backup after experiencing a disaster