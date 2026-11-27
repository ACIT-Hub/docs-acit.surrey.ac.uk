# Visualising Metrics

Making system metrics readily accessible through a well-considered set of dashboards is an effective way to streamline both routine analysis and responses to faults. The key is to have appropriate dashboards prepared in advance, rather than creating them only when a problem occurs. Dashboards can, of course, be refined and expanded over time as their use reveals overlooked edge cases or additional monitoring requirements. However, having a useful set of dashboards already in place means that investigating an issue can begin immediately, without valuable time being lost assembling the necessary views in response to an incident.

## Deciding Which Dashboards to Create

In [Section 2](approaches-and-tools.md) (Monitoring Approaches & Tools), we briefly introduced the three groups with an interest in dashboards: users, RIEs and stakeholders. But what dashboards might each group require, and what questions should their dashboards help answer?

<!-- These might be adjusted slightly as we receive examples from RIEs. -->

- **Users/Jobs**: Typical HPC users are unlikely to explicitly request dashboards, although HPC centres are increasingly making them available to help researchers understand how their individual jobs are performing. With research funders paying greater attention to the carbon cost of research, users are also beginning to seek greater awareness of the energy consumed by their workloads. Other comparisons, such as requested versus actual resource usage, can help users understand whether their job submissions are appropriately configured and encourage more efficient resource requests.
    - **Groups/Grants**: Institutions that operate project or research-group-based compute allocations may also provide group-level dashboards. These allow group owners to quickly assess how their allocated resources are being used across their teams. Rather than focusing on individual jobs, these dashboards are likely to aggregate job-level metrics by grant or project, potentially allowing further breakdowns by individual user.
- **RIEs**: RIEs are likely to require the largest collection of dashboards. They are responsible for monitoring the operational environment, identifying incidents and investigating their causes. This often involves correlating live metrics and logs from multiple components to narrow down the source of an active problem. The aim should be to provide dashboards organised around specific areas of concern, highlighting the most significant metrics and making it easy to identify anything behaving abnormally.
    - **Compute Nodes**: Are filesystems approaching capacity? Is I/O performance degrading?
    - **Storage**: Are there signs of congestion, packet loss or unusual traffic?
    - **Network**: Are any nodes behaving abnormally? Are CPU, memory or GPU resources under pressure?
    - **Job Scheduling**: Are jobs spending unusually long in the queue? Are jobs failing or being held?
    - **Power & Cooling**:Are temperatures, power consumption or cooling demands outside expected ranges?
    - **Infrastructure**: Are services, hardware components or management systems unavailable?
- **Stakeholders**: Stakeholders are generally more interested in strategic concerns such as costs, resource efficiency and demonstrating value for money. They may not necessarily access dashboards themselves, instead relying on RIEs to provide the information needed to justify expenditure, demonstrate demand or support purchasing decisions.
    - **Energy**: Energy is one of the largest ongoing costs for many HPC centres. With energy prices subject to considerable volatility, stakeholders may want to understand why consumption has increased over a particular period. A dashboard showing increased hardware utilisation alongside corresponding changes in user activity can help explain the increase and demonstrate that additional energy consumption is associated with greater use of the facility.
    - **Utilisation**: When funding becomes available to purchase additional hardware, the window for making purchasing decisions can be very short. Having dashboards that demonstrate long-term utilisation trends can help establish demand, complement explicit user requests for hardware and provide evidence to support purchasing decisions. This can also help ensure that new hardware, and replacement systems, are appropriately sized to meet future requirements.

## Creating Dashboards with Grafana

<!--
    - Brief Intro to manual approaches
    - AI MCP efficiency
-->

## Real-world Accelerated Compute Dashboards

<!--
    - What does a good dashboard look like?
    - Gallery?
-->