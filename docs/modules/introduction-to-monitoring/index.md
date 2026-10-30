# Introduction to Monitoring for Accelerated Compute
## Overview

Monitoring is the process of regularly collecting information about how a project, service, or system is performing. As a junior Research Infrastructure Engineer (RIE), you may be responsible for clusters, GPU nodes, storage systems or research platforms that many users depend on. Without monitoring, problems can go unnoticed until users complain, resources can be wasted, and it becomes very hard to understand how your systems are really behaving. Monitoring allows you to spot issues early, make evidence-based decisions, and explain what is happening to colleagues and users.

This module introduces the fundamentals of monitoring from an RIE perspective. You will explore what monitoring is and why it matters in HPC and accelerated compute environments, the kinds of metrics you should care about (such as CPU, GPU, memory, storage and service health), and the role RIEs play in designing, maintaining and interpreting monitoring systems. The module also provides a practical introduction to Grafana as a common tool for visualising metrics, using realistic dashboards and case studies to show how monitoring helps diagnose problems and plan improvements. It will also touch on ethical and environmental aspects of monitoring.

???+ note "Who this module is for"
    This module is for junior Research Infrastructure Engineers (RIEs) and sysadmins who expect to work with HPC and accelerated compute. However, we recommend you familiarise yourself with the material within [Introduction to Accelerated Compute](../introduction-to-accelerated-compute/index.md) first.

???+ tip "Learning outcomes"
    By the end of this module, you will be able to:

    - Explain what is meant by monitoring clusters and the role of RIEs
    - Configure new exporters and dashboards within the Grafana stack
    - Interpret dashboards and identify appropriate thresholds and responses
    - Discuss how monitoring can assist with ethical and sustainability considerations

???+ tip "Competencies"
    This module supports the development of levels 1-2 of the following [DIRECT][direct] skills: 
    
    - [Job scheduling and resource management][direct-job_scheduling]. 
    - [Performance profiling and optimisation][direct-performance_profiling]. 
    - [Cost modelling][direct-cost_modelling]. 

[direct]: https://directframework.com/framework/skills_and_competencies/#computing-infrastructures-and-systems
[direct-job_scheduling]: https://directframework.com/framework/skills/job-scheduling-and-resource-management/
[direct-performance_profiling]: https://directframework.com/framework/skills/performance-analysis-and-optimisation/
[direct-cost_modelling]: https://directframework.com/framework/skills/cost-modelling/


## Sections

??? info "1. Introduction"
    - What is monitoring, and why is it performed.
    - What is monitored.

    [ Go to section](introduction.md)

??? info "2. Monitoring Approaches and Tools"
    - What does the architecture of a classic monitoring setup look like.
    - Who may have interest in monitored metrics.

    [ Go to section](approaches-and-tools.md)

??? info "3. Prometheus & Exporters"
    - pending (E.G. now may split content across Victoria Metrics)

    <!-- [ Go to section](prometheus.md) -->

??? info "4. Grafana"
    - pending (Awaiting dashboard examples to guide development)

    <!-- [ Go to section](grafana.md) -->

