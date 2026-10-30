# Introduction

By the end of this chapter, you will be able to:

- Explain the role of a Research Infrastructure Engineer in monitoring accelerated compute infrastructure.
- Identify components of accelerated infrastructure which may be monitored.

## What is Monitoring?

Monitoring is the process of regularly collecting information about how a project, service, or system is performing.

By watching or analysing this data, sometimes supported by dashboard, automated notifications and other tools, issues can be caught early before they cause a detriment to users (or budgets). Monitoring helps turn potential crises for RIEs, into almost routine work.


???+ note "A monitoring scenario:"

    An RIE arrived one morning to find their cluster under heavy load with several critical services behaving erratically. Without monitoring, the root cause would have only became obvious once users’ jobs began to fail causing them to start filing tickets.
    
    Because their team maintain monitoring dashboard, which provide continuous high-level view of storage health, node status and service metrics, the RIE was able to identify the early warning signs of a slowly degrading filesystem, which had started the previous evening. This allowed them to perform a quick investigation to confirm the issue, before performing a controlled failover allowing the problem to be resolved before most users noticed.
    
    Monitoring avoided a chaotic morning of reactive troubleshooting and replaced it with a brief planned intervention.
    
    <!-- @Todo: This anecdote is synthetic, it would be good to create a more authentic one with greater detail -->

## What can be Monitored?

A wide range of metrics on a HPC can be monitored: 

- **Nodes**: Are nodes responsive? Catch a failing login node before user’s report it.  
- **Scheduler**: Do job statistics look healthy? Even if users can login, the scheduler might highlight an issue causing jobs to unexpectedly queue or fail. 
- **Storage & I/O**: Are drives reporting any errors? This usually precedes a failure. 
- **Interconnect & Network**: Even if nodes look healthy, perhaps their communication is deteriorating. Network issues might even include the external internet connection failing. 
- **Facility Power & Cooling**: If a facilities cooling or power goes down, it may be necessary to carefully take nodes offline to avoid damaging hardware or losing data.

Whilst Research Infrastructure Engineers, and the wider operations team, are interested in monitoring system health, the same monitoring infrastructure can provide valuable information to other parties. 

Users may benefit from monitoring their workload statistics, to understand the performance of their jobs. Perhaps they are requesting more resources than necessary, and can adapt their job submissions to aide their scheduling. 

Likewise, stakeholders among management are often keen to monitor utilisation of their investment in the cluster. This could help to evidence how a cluster should be extended, or opportunities to improve sustainability (and reduce energy costs) by taking lower utilised nodes offline during quiet periods. 

As a junior RIE you may be expected to setup new dashboards from scratch, or configure initialisation workflows to automatically extend monitoring to new hardware. Likewise, you will need to understand the environment and chain of command locally, so that you can respond appropriately when monitoring highlights a metric requiring action.

<!-- @Todo: Ethical/Sustainability considerations -->

## Keypoints

- Monitoring is the processes of regular collecting metrics from accelerated compute infrastructure, to aide decision making and streamline the diagnosis and resolution of faults.
- Monitoring metrics extend from the scheduling software, to hardware metrics and even in some cases to the facility housing the infrastructure.
- Users, RIEs and Stakeholders each have an interest in different collected metrics.
- Junior RIEs may be expected to both create and respond to dashboards.

<!-- @Todo: These require review/refinement, quickly drafted when porting page from word doc. -->