# Approaches & Tools

<!-- This whole chapter needs a review with victoria metrics in mind -->

By the end of this chapter, you will be able to:

- Explain the topology of a monitoring software stack.
- Match metrics to the hardware (and exporter) that produces them.
- Identify the parties that may be interested in collected metrics.

## How is Monitoring Performed?

Monitoring generally involves a full software stack. Multiple exporters, collect and export different metrics from a cluster, metrics are gathered and collated in a monitoring database, which can then be visualised and alert conditions configured via a dashboard. 

These three components can be interchangeable, however most HPC has now standardised around a Prometheus monitoring database, with Grafana dashboards. Prometheus has a wide range of supported exporters to cover most typical metrics and Grafana can consume data from a wide range of sources in addition to Prometheus. More recently Victoria Metrics has appeared as a challenger to Prometheus, however it is compatible with Prometheus' exporters and supports the same query language, so the skills taught within this course are transferable.

<!-- @Todo: Logging?? -->


???+ tip "Other Tools"

    Historically, many different tools were more commonly used for monitoring; e.g. Ganglia, Nagios/Icinga, LDMS, Zabbiex, Elastic Stack (ELK), or vendor specific tools such as HPE Performance Cluster Manager, NVIDIA DGCM. 

    <!-- @Todo: More detail, how prominent are ELK, Cluster cockpit? -->
    
    
The combination of Prometheus and Grafana became popular in HPC due to its flexible time-series metrics and open-source codebase enabling a wide range of supported exporters and rich dashboards. Some of these other tools may still be used, possibly in combination with Prometheus and Grafana, locally at your institution. 

<!-- @Todo: Review whether this renders correctly -->

<div class="img-box" markdown>
![Monitoring stack topology showing exporters in an HPC cluster and server room sending data to a Prometheus database. Prometheus feeds Grafana dashboards, which are used by users, RIEs, and stakeholders.](../../assets/images/monitoring/monitoring-stack-topology.png)
A diagram showing a generic network topology of a monitoring stack. The exporters within the HPC cluster and data centre (left) communicate with a Prometheus database, which is visualised to 3 different groups via Grafana Dashboards.
</div>
*Figure 1*

## Monitoring Database & Exporters

Prometheus is an open-source database and related collection of tools, specifically designed to support monitoring. It offers tools for the full-monitoring stack, including dashboards and alerts, however due to its interoperability, it is most often used for its server which acts as a central database for storing and querying monitoring metrics. Victoria metrics is very similar in what it offers, however it has been designed to maximise compatibility with an existing Prometheus monitoring stack.

Prometheus operates with a pull-based scraping model, where it scrapes from attached exporters at regular intervals. It is also possible to configure for how long the metrics will be retained within the database. 

There are hundreds of exporters which are compatible with Prometheus and Victoria Metrics, many which are irrelevant to most HPC systems. Most can be configured to select precisely which metrics to collect and potentially also the collection frequency.  

Below we introduce some of the core exporters you may come across.

### Node Exporter

As the name implies, [node exporter](https://github.com/prometheus/node_exporter) exports system metrics from HPC nodes relating to their hardware and operating systems. This can include CPU, memory, storage, networking, power and more metrics from each node within a cluster.

### SLURM Exporter

Whilst you could manually probe SLURM for scheduler metrics, [SLURM exporter](https://github.com/sckyzo/slurm_exporter) exposes all the information you might want from the scheduler, alongside some sample Grafana dashboards. If your system instead uses Kubernetes, a SLURM exporter would be redundant.

### NVIDIA DCGM (GPU) Exporter 

If a cluster contains NVIDIA GPUs it’s likely to use NVIDIA’s [DCGM Exporter](https://github.com/NVIDIA/dcgm-exporter) which exposes detailed metrics from NVIDIA GPUs via the Data Centre GPU Manager (DCGM). This includes GPU utilization, memory usage, temperature, power draw, ECC errors, NVLink status and other hardware health and performance data from each GPU across the cluster. 

### AMD’s Service Monitor 

Likewise, if a cluster contains AMD GPUs, a [service monitor](https://instinct.docs.amd.com/projects/gpu-operator/en/latest/metrics/prometheus.html) can be created to export relevant AMD GPU information produced by their Device Metrics Exporter. Information includes temperature, utilisation, memory usage, power consumption, PCIe bandwidth and performance metrics. 

### Infiniband Exporter 

If a cluster utilises InfiniBand for high-bandwidth communication between nodes, it would make sense to add monitoring the entire InfiniBand fabric. These metrics would include the count of occurrences for each specific errors, and potentially utilisation information such as the number of packets sent. 

### Lustre Exporter

The file systems used by a cluster, such as Lustre, will have exporters to monitor error counts and access metadata to help catch hardware failures and identify problematic jobs. 


### Bespoke Exporters

Writing a small script to export data may also be carried out due to it’s simplicity.  Prometheus provides a [Python package](https://github.com/prometheus/client_python), making it possible to write an exporter in less than 10 lines of code. 

This can be great if you’re using small external sensors, to monitor server room metrics such as temperature and capacity which don’t have native exporters. Some clusters will monitor the room temperature, as it may become necessary to shutdown a cluster during heat waves if the cooling is unable to cope. 

-------------

The above summary is not exhaustive, other hardware solutions will normally have exporters which can be installed to monitor them, likewise in some cases there are multiple suitable exporters. 

## Grafana

<!-- @Todo: Feels like we need a high-level intro to Grafana/dashboards here -->

<!-- @todo Replace this with a submit case-study dashboard -->
<div class="img-box" markdown>
![A Grafana dashboard showing...](../../assets/images/monitoring/generic-grafana-dashboard.png)
An example Grafana Dashboard from a HPC system.
</div>
*Figure 2*

Grafana is a query and visualisation front-end, which allows users to import metrics from Prometheus and other sources and configure bespoke dashboards with a large range of visualisation components to meet requirements. It does not collect or store any metrics itself.

In addition to building dashboards from scratch, it is possible to import (and modify) dashboards. Public dashboard, available from [Grafana's website](https://grafana.com/grafana/dashboards/) are sometimes imported as starting points, but they are often too general (showing every metric an exporter provides) and are typically customized to surface only the most actionable information for a given system or team.

Grafana can also be used to configure alert conditions. Grafana-managed alerts are particularly useful when metrics come from sources beyond Prometheus. In addition, Grafana can display and help manage Prometheus alert rules.

Despite its front-end focus, both Grafana dashboards and alerting rules can be exported and provisioned as plain-text JSON or YAML, making them reproducible across systems.


!!! question "Exercise 1: Setup"

    *This exercise will walk you through running a set of docker containers to host a small local monitoring stack for learning. It's important we set it up early, so that metrics have time to gather that can be queried later on.*
    
    Download the training environment from [here](monitoring-exercises.zip), and extract the files. Open a terminal in the root of the extracted files so that docker can be started.

    `docker compose up -d` will initialise and run the training environment.

    - Prometheus is running at [http://localhost:9090](http://localhost:9090)
    - Grafana is running at [http://localhost:3000](http://localhost:3000)
        - Username and password are both `learning`
        - Prometheus has already been configured
    - Node exporter is running at [http://localhost:9100](http://localhost:9100)
        - This has not been added to Prometheus *(That is the first exercise)*.

    **Check that each of these pages are reachable!**

    If Prometheus or Grafana require restarting during later exercises, you can shutdown and restart the containers with.

    ```sh
    docker compose down
    docker compose up -d
    ```

    Also remember to shutdown the containers when you have completed all of the exercises.

## Keypoints

- Different accelerated compute components use different exporters to expose their metrics.
- Prometheus and Victoria Metrics are time-series databases commonly used in monitoring software stacks, capable of fetching and storing data produced by exporters.
- Grafana is a dashboard tool commonly used for visualising collected metrics.

<!-- @Todo: These require review/refinement, quickly drafted when porting page from word doc. -->