# Using Prometheus & Exporters

<!-- This chapter needs adapting to see whether we can integrate victoria metrics smoothly -->

## Setting Up a New Exporters

Prometheus is provided as a standalone executable, which reads from a `.yml` configuration file which may refer to other `.yml` rule files. Prometheus must be restarted if these `.yml` files are changed, as only a small subset of fields (e.g. retention) are runtime reloadable. 

If running Prometheus, without any arguments it will look for the configuration `prometheus.yml` in the working directory, akin to: 

```sh 
./prometheus --config.file=prometheus.yml 
``` 

In practice, your Prometheus server will likely be configured to run as a service on a server rather than your local machine, so that it’s accessible to the whole team and automatically restarts. Check locally with your team about how to access Prometheus, and how it can be restarted when reconfiguring. For teaching purposes, here we’ll continue running it manually on the command line.

When running, by default it will serve a status page at http://localhost:9090, which provides querying and basic dashboard facilities for collected metrics. We’ll cover structuring Prometheus queries later in this chapter, as they can be useful when investigating spurious metrics.

Each exporter that Prometheus scrapes from, will host a small web server with a `/metrics` path. The location of each exporter must be configured within Prometheus’ configuration `.yml`.

If we download and execute [node_exporter](https://github.com/prometheus/node_exporter), which reports metrics about a local node, by default it will serve http://localhost:9100/metrics (each exporter will likely have a different default port, although this will often be configurable).
 
Under the `scrape_configs` section of the Prometheus config `.yml`, we can then specify the exporter to be scraped:

```yml 
  # Specify a new job for our node exporter 

  - job_name: "node exporter" 

    scrape_interval: 1s # Override global scrape interval 

    static_configs: 

    - targets: # The target(s) to scrape from 

      - "localhost:9100"  

      labels: # Labels to apply to metrics from this job 

        environment: "production" 
``` 

Within this block it's possible to override the global defaults, such as `scrape_interval` for a particular job. Likewise, multiple targets can be scraped as part of the same job and labels assigned to all metrics that are scraped via the job.



???+ tip "`.yml` Mistakes"

    As the configuration is .yml indentation is very strict. If Prometheus is unable to read your updated configuration, an error will be produced, and it will fail to start. This error will usually specify either the affected line or section of the configuration where parsing failed. 

    E.g. the below error was produced when labels was indented by 2 additional spaces 
    
    ``` 
    time=2026-09-30T10:33:43.292+01:00 level=ERROR source=main.go:768 msg="Error loading config (--config.file=prometheus.yml)" file=/home/rob/prometheus/prometheus-3.15.0.linux-amd64/prometheus.yml err="parsing YAML file prometheus.yml: yaml: unmarshal errors:\n  line 41: cannot unmarshal !!map into string" 
    ``` 

    Likewise, it’s possible to add a spurious `-` to labels, leading to labelling a null target. This will build, but the label won’t be present in any collected data. 



## Querying Prometheus Metrics

Labels can be queried with PromQL, to find all linked metrics, e.g. for the above 
 
``` 
{environment="production"} 
``` 

Within Prometheus, selecting "Query" in the navigation bar opens the query view. If the above statement is entered and produces results, the label is working (or exists elsewhere). 

<!-- @Todo brief explainer on PromQL, how to structure basic queries -->

Prometheus also allows PromQL to be used to define rules, to pre-calculate dependent metrics (recording rules), and to create alerts (alerting rules). These can be defined within separate rule `.yml` files, and will be evaluated at the frequency defined by the evaluation_interval in the main configuration file.

### Recording Rules

Recording rules, which calculate dependent metrics, can avoid the need to re-calculate expensive dependent metrics every time a dashboard is loaded.
 
The below demonstrates an example rule file which defines the record `code:prometheus_http_requests_total:sum` to sum the total number of HTTPS requests to the Prometheus server over time, for each response code.

```yml
groups:

  # Name of the group, must be unique within the rule file

- name: example

  interval: 1m # How often the rule is evaluated, overrides global.evaluation_interval

  rules:

    # Name of the record to produce  

  - record: prometheus_http_requests_total:sum_by_code  

    # PromQL expression to evaluate at each evaluation interval.

    expr: sum by (code) (prometheus_http_requests_total) 
```

If this rule file is saved as `http_sum.yml` in a `rules` subdirectory, it can be added to Prometheus’ configuration within the `rules_files` section.

```yml

rule_files: 

  - "rules/http_sum.yml" 

```

Now when Prometheus is started metrics with the name `prometheus_http_requests_total:sum_by_code` will be collected.

### Alerting Rules

<!-- @Todo: May want to configure alerts within Grafana -->
Alerting Rules, define conditions using Prometheus expressions which fire alerts to external services (e.g. Slack) via Prometheus’ stand-alone Alert Manager.

When configuring alerts, it’s important to follow two principles: 

Alerts should not trigger frequently; if alerts are triggering regularly, they will lead to alarm fatigue whereby RIEs become desensitised to alerts. 

Alerts must be actionable; An alert which does not lead to an imminent response, should not be an alert. Many metrics may naturally reach close to maximum during peak periods, this is not something actionable, rather it’s something that stakeholders may review in future when considering expanding the cluster. 

When defining an alert, the primary consideration is the condition that should trigger it. This typically involves setting a threshold for a specific metric, potentially requiring that threshold to be exceeded for a defined period before an alert is raised and sent to the appropriate people or channel. 

Because alert thresholds are highly dependent on local priorities and operational requirements, we recommend that they are agreed upon by the team responsible for receiving and responding to the alert. Likewise, the communication channel for alerts is likely to already be agreed at a team level, such as a Slack channel or mailing list. 
