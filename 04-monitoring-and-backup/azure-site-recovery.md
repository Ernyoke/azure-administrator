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

## Replication vs Backup

- Azure Site Recovery (ASR) provides disaster recovery through replication and failover, not long-term backup retention
- Replication maintains recovery copies that can be started when the primary workload becomes unavailable
- Backup retains historical recovery points to recover deleted, corrupted or older data
- Use both when the workload needs rapid disaster recovery and historical retention
- RPO measures acceptable data loss as time; actual recovery point age matters, not just configured replication frequency
- RTO measures acceptable service downtime, including failover, application startup, DNS changes and validation

## Configure Azure-to-Azure Replication

- Create a Recovery Services vault, normally outside the source region
- Select the source VMs and a supported target region
- Configure target resource groups, networks, subnets, VM sizes and availability options
- Use a cache storage account in the source region to stage replication changes
- Site Recovery installs and manages the required replication components for supported Azure VMs
- Configure replication policy retention and application-consistent recovery point settings
- Check replication health and complete initial synchronization before relying on recovery
- Target-region VM quota and capacity are still required when failover creates the recovered VMs
- Plan how clients reach the recovered application, including DNS, load balancers and public IP addresses

## Test Failover

- A test failover creates test VMs from a recovery point without stopping ongoing replication
- Use an isolated test virtual network to avoid IP conflicts and accidental interaction with production
- Validate application startup, dependencies, authentication and network access
- Clean up the test failover after recording the result so temporary resources do not remain running
- A successful test does not switch production traffic or complete an actual failover

## Failover, Reprotect and Failback

- Choose a recovery point based on the required consistency and recovery point objective
- Latest processed recovery points generally start faster because no additional replication data must be processed
- Application-consistent points can reduce application recovery work but may be older
- During Azure-to-Azure failover, optionally attempt to shut down the source VM first when it is reachable
- Validate the recovered application and redirect client traffic as required
- Commit the failover after accepting the chosen recovery point; the previous recovery point choice cannot then be changed
- Reprotect configures replication from the new primary region back toward the original region
- Fail back after reverse replication is healthy and the original region is ready
- A recovery plan groups machines into ordered startup groups and can include automation and manual steps
- Example recovery order: identity and database services, application tier, then web tier