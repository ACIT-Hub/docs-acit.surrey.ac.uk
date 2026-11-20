# Using Prometheus & Exporters

Prometheus and Victoria Metrics are inherently time series databases, which gather and store metrics, labelling them to help identify their source. Lets breakdown some of these new terms:

| Term        | Explanation                                                                          |
| ----------- | ------------------------------------------------------------------------------------ |
| Metric      | A named measurement, such as `node_hwmon_temp_celsius`.                              |
| Time series |	A sequence of measurements for a particular metric and unique combination of labels. |
| Label       | A key-value pair that describes a metric, such as `environment="production"`.        |

It's worth clarifying the distinction between a metric and a time series, `node_hwmon_temp_celsius` is a single metric, which can represent many different time series as a node is likely to contain many different temperature sensors and a cluster is likely to contain many nodes.

## Setting Up a New Exporters

Prometheus is provided as a stand-alone executable, which reads from a `.yml` configuration file which may refer to other `.yml` rule files. Prometheus must be restarted if these `.yml` files are changed, as only a small subset of fields (e.g. retention) are runtime reloadable. 

If running Prometheus, without any arguments it will look for the configuration `prometheus.yml` in the working directory, akin to: 

```sh 
./prometheus --config.file=prometheus.yml 
``` 

In practice, your Prometheus server will likely be configured to run as a service on a server rather than your local machine, so that it’s accessible to the whole team and automatically restarts. Check locally with your team about how to access Prometheus, and how it can be restarted when reconfiguring. For teaching purposes, here we’ll continue running it manually on the command line.

When running, by default it will serve a status page at [http://localhost:9090](http://localhost:9090), which provides querying and basic dashboard facilities for collected metrics. We’ll cover structuring Prometheus queries later in this chapter, as they can be useful when investigating spurious metrics.

Each exporter that Prometheus scrapes from, will host a small web server with a `/metrics` path. The location of each exporter must be configured within Prometheus’ configuration `.yml`.

If we download and execute [node_exporter](https://github.com/prometheus/node_exporter), which reports metrics about a local node, by default it will serve [http://localhost:9100/metrics](http://localhost:9100/metrics) (each exporter will likely have a different default port, although this will often be configurable).
 
Under the `scrape_configs` section of the Prometheus config `.yml`, we can then specify the exporter to be scraped:

```yml {title="prometheus.yml#scrape_configs"}
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

    As the configuration is `.yml` indentation is very strict. If Prometheus is unable to read your updated configuration, an error will be produced, and it will fail to start. This error will usually specify either the affected line or section of the configuration where parsing failed. 

    For example, the below error was output when `labels` was indented by 2 additional spaces 
    
    ``` 
    time=2026-09-30T10:33:43.292+01:00 level=ERROR source=main.go:768 msg="Error loading config (--config.file=prometheus.yml)" file=/home/rob/prometheus/prometheus-3.15.0.linux-amd64/prometheus.yml err="parsing YAML file prometheus.yml: yaml: unmarshal errors:\n  line 41: cannot unmarshal !!map into string" 
    ``` 

    Likewise, it’s possible to add a spurious `-` to `labels`, leading to labelling a null target. This will build, but the label won’t be present in any scraped data. 


!!! question "Exercise 2: Adding a new scrape target to Prometheus"

    Update `prometheus/prometheus.yml` to read from node exporter which can be accessed by Prometheus at `node-exporter:9100`.

    As explained earlier, add a label `acit` to the node exporter scrape target and override it's scrape interval to be one second.

    *Once node exporter has been added as a scrape target to Prometheus, it will take time for Prometheus to gather metrics from the new source. To quickly check whether it has been configured correctly, visit [http://localhost:9090/targets](http://localhost:9090/targets) where it should be listed with the name you gave it.*
    
    
    ??? tip "Prometheus not running after restart?"
        When making changes to Prometheus' configuration, it's possible to introduce a syntax error which will prevent it from starting.

        If Prometheus doesn't appear to be running, try checking the output logs which might highlight a syntax error.

        ```sh
        docker compose logs prometheus
        ```
    
    ??? success "Solution"
        The below should be appended to `prometheus/prometheus.yml` (under `scrape_configs:`). `job_name` may be different however it must be remembered.

        ```yml
          - job_name: 'node_exporter'
            scrape_interval: 1s # Override global scrape interval
            static_configs:
              - targets: ['node-exporter:9100']
              labels: # Labels to apply to metrics from this job
                environment: "acit"
        ```

        Prometheus must then be restarted for the change to take effect 

        ```sh
        docker compose down
        docker compose up -d
        ```

        If working correctly, [http://localhost:9090/targets](http://localhost:9090/targets) should now contain a box titles "node_exporter" which will be shown as "up" in the top right corner.
        

??? note "Victoria Metrics Differences"

    Victoria Metrics can read a Prometheus scrape configuration, however it's provided at runtime differently and served on a different port.
    
    ```sh
    ./victoria-metrics-prod -selfScrapeInterval=10s -promscrape.config=prometheus.yml
    ```
    
    It will then be available at 
    
    [http://localhost:8428](http://localhost:8428)
    
    *Things will be a little bit different if the cluster version of Victoria Metrics is being used, so check with your team to understand your local configuration.*

## Querying Metrics with PromQL


<!-- https://prometheus.io/docs/prometheus/latest/querying/basics/ -->

Metrics can be queried in both Prometheus and Victoria Metrics (including via Grafana) with PromQL (Prometheus Query Language).

### Selecting Metrics


#### Select all metrics matching a label

To find all linked metrics, e.g. for production environment label before, we can use the following query:
 
```PromQL  {title="PromQL Query"}
{environment="production"} 
``` 

Within Prometheus, selecting "Query" in the navigation bar opens the query view. If the above statement is entered it should produce results similar to this: 

<div class="img-box" markdown>
![The Prometheus Query interface, showing the table of results for querying the label environment=production.](../../assets/images/monitoring/promql-first-query.png)
The result of querying `{environment="production"}` within Prometheus, after setting up node exporter as explained earlier.
</div>
*Figure 3*

This basic query however is too broad, it returns all metrics with the production environment label which is everything output by the exporter we created.

#### Selecting a particular metric

To return all time series for a specific metric, that metric's name can be queried. For example for hardware temperatures there is the metric `node_hwmon_temp_celsius`. 

```PromQL  {title="PromQL Query"}
node_hwmon_temp_celsius
```

Returns multiple time series, each with 3 labels:

- `instance` identifies the monitored node.
- `chip` identifies a hardware monitoring chip.
- `sensor` identifies an individual temperature sensor within a `chip`.

Different hardware configurations will have different numbers of chips available per node, and in a cluster there will likely be a unique instance per node. Hence, why multiple time series with different labels are returned.

#### Filter a metric using labels

We can filter the query based on any of the labels shown within the curly brackets `{ }` of the results. For example, we can update to only query for the `platform_coretemp_0` chip's sensors 1-3.

```PromQL  {title="PromQL Query"}
node_hwmon_temp_celsius{chip="platform_coretemp_0", sensor=~"temp[1-3]"}
```

Adding `chip="platform_coretemp_0"`, filtered the query to only return metrics for that specific chip and adding `sensor=~"temp[1-3]` added a more advanced regular expression (Regex) pattern matching filter for sensors matching `temp1`, `temp2` or `temp3`.


???+ tip "Regex Notes"

    Regular Expressions are a powerful dialect for defining pattern matching criteria, used widely across all programming languages.
    
    If you're new to Regex and need to describe a pattern, you should review the [Regex reference guide](https://www.regular-expressions.info/refcharacters.html) and test your pattern works with one of many [online Regex testing tools](https://regex101.com/) which allow a Regex statement to be tested live on different test strings. These online testing tools often also provide hints to explain the rules being applied.

    PromQL RegEx statements are fully anchored (they don't permit additional leading/trailing characters), hence `temp[1-3]` is equivalent to `^temp[1-3]$` and would not match the string `"temp11"`

Four string matching operators can be used for labels:

| Operator | Meaning                | Example                       |
| -------- | ---------------------- | ----------------------------- |
| `=`      | Exact match            | `chip="platform_coretemp_0"`  |
| `!=`     | Does not exactly match | `chip!="platform_coretemp_0"` |
| `=~`     | Regex match            | `sensor=~"temp[1-3]`          |
| `!~`     | Does not match Regex   | `sensor!~"temp[1-3]`          |

Therefore, the previous sensor filter can be inverted by replacing `=~` with `!~`:

```PromQL  {title="PromQL Query"}
node_hwmon_temp_celsius{chip="platform_coretemp_0", sensor!~"temp[1-3]"}
```

It will now only show sensors with `temp4`, `temp5` and higher.

### Functions & Operators

PromQL also supports a range of functions and operators which can be used within queries to process metrics, either by performing operations across a time series or combining multiple time series.

All traditional arithmetic operations can be performed between time series (e.g. addition, through modulo [`%`] and exponent [`^`]), as can comparison operators such as equality [`==`, `!=`] and greater/less-than [`>`, `<`, `>=`, `<=`], set logic (e.g. intersection [`and`], union [`or`], compliment [`unless`]) and aggregation operations (e.g. sum, average).

It's worth reviewing the full [PromQL operators documentation](https://prometheus.io/docs/prometheus/latest/querying/operators/).

Functions are even more widespread, covering basic mathematical functions (e.g. `abs()`) and aggregations (e.g. `avg()`) through to date and histogram operations. 

It's worth reviewing the full [PromQL functions documentation](https://prometheus.io/docs/prometheus/latest/querying/functions/).

#### Aggregations

Aggregations are probably one of the most useful features of PromQL for an RIE, allowing you to answer questions about groups of time series (e.g. across multiple nodes) or reduce the resolution of a time series.

For example:

```PromQL  {title="PromQL Query"}
max(node_hwmon_temp_celsius)
```

Returns the maximum value across all time series within the `node_hwmon_temp_celsius` metric. Prometheus displays the overall maximum in the Table tab when using an instant query. In the Graph tab we can instead see how this maximum changes over time as it will be processed as a range query.

The most commonly used aggregation functions are:

- `sum(v)` for totals.
- `avg(v)` for averages.
- `min(v)` for minimum values.
- `max(v)` for maximum values.
- `topk(k, v)` largest `k` elements
- `bottomk(k, v)` smallest `k` elements
- `count(v)` the number of time series within a metric

Aggregations can be grouped according to a label with `by`, to produce multiple max, for example:

```PromQL  {title="PromQL Query"}
max by(chip) (node_hwmon_temp_celsius)
```

Displays the maximum temperature across all sensors for each chip.

<div class="img-box" markdown>
![A query results interface showing a line graph with two plots hovering around 35 and 50 respectively.](../../assets/images/monitoring/promql-grouped-aggregation.png)
The result of a Prometheus query, grouping `node_hwmon_temp_celsius` by chip.
</div>
*Figure 4*

Alternatively, you can use `without` to remove specific labels from the output. The `without` aggregation therefore combines series that differ only by their sensor label. For example:


```PromQL {title="PromQL Query"}
max(node_hwmon_temp_celsius) without (sensor)
```

Returns a plot matching Figure 4, as `max` has been applied across all sensors of each chip, as the `chip` and `instance` labels were retained. 

In practice, you're more likely to group by the `instance` label in a cluster with multiple nodes so that nodes can be compared to expose outliers.


#### Time-based Queries

When working with time series metrics, RIEs will often want to investigate how metrics change over time.

`rate()` calculates the average per-second rate of increase of a counter over a specified time window. For example, the following query calculates the average network receive rate over the previous five minutes:

```PromQL  {title="PromQL Query"}
rate(node_network_receive_bytes_total[5m])
```

These range queries show how a value changes over time.

There are four types of metrics you might find, which each require different time-based query handling.

| Metric Type | What it measures | Typical use | Common PromQL functions |
| ----------- | ---------------- | ----------- | ------------------ |
| Counter | A value that only increases, except when reset | Network traffic, CPU time, requests | `rate()`, `increase()` |
| Gauge | A value that can increase or decrease | Temperature, memory usage, CPU utilisation | `avg_over_time()`, `max_over_time()` |
| Histogram | Observations grouped into buckets | Request durations, operation latency | `rate()`, `histogram_quantile()` |
| Summary | Statistical observations over time | Request durations and quantiles | *Usually queried directly* |

*Histograms and summaries are more advanced metric types. Their use and interpretation are beyond the scope of this introduction, but it is useful to recognise them when inspecting unfamiliar metrics.*


!!! question "Exercise 3: Using PromQL to query metrics"

    #### A) Instant Vector Query

    Enter the following query, what does this show?

    ```promql
    up
    ```

    ??? success "Solution"
        In table view, it will show a table with 2 rows, representing a metric for the two scrape jobs; `prometheus` the default exporter, and `node-exporter` that you setup during the previous exercise.

        The table shows their instant value, which are hopefully both `1` as they should be running.

    #### B) Filter Query

    Amend the previous query so that it only returns the `up` metric for your `node-exporter`.

    ??? success "Solution"
        You can filter by specifying any of the labels unique to `node-exporter` inside curly bracket, e.g.

        ```promql
        up{job="node-exporter"}
        ```

        or 

        ```promql
        up{instance="node-exporter:9100"}
        ```

    #### C) Range Vector Query

    Enter the following query, and select the "graph" tab to visualise the results.

    ```promql
    rate(prometheus_http_requests_total[5m])
    ```
    Try increasing and decreasing the time range.

    What does this show?

    ??? success "Solution"
        `prometheus_http_requests_total` is an instant vector metric, it acts as a counter of the total number of page visits labelled according to the accessed URL (`handler`) and the http response code (`code`).

        The `rate()` function calculates the rate of increase with a sliding window equal to the specified time-range. Therefore, decreasing the time-range (e.g. `[1m]`) will make the graph more spiky, as each visit will affect less sliding windows, and increasing it will have the opposite effect.

        <!-- I don't really like this, but feels like we should talk about selecting an appropriate time-range. -->
        The "best" time-range to use, would depend on the use-case and deviation, although a lower value is more representative of the truth.

    #### D) Aggregation

    Update your query, to show the combined sum of all `prometheus_http_requests_total` metrics over time, to produce a graph with a single line representing page views.

    Update this query to group requests according to `handler`, ignoring `code`, to more clearly identify the most accessed pages at any time.

    Now extend it once more, to filter so that only API calls are returned.

    ??? tip "Matching Hint"
        `.` is the Regex symbol which will match any character.
        `*` is the Regex symbol which requires the previous element of the Regex pattern to be matched 0 or more times.

        So a suitable Regex string to match only `handler` labels for API calls would be `/api/.*`.

    Which API call dominates http requests? Which is second?


    ??? success "Solution"
        To sum all `prometheus_http_requests_total` metrics, over time, wrap the earlier command with the `sum()` function.

        ```promql
        sum(rate(prometheus_http_requests_total[5m]))
        ```
        
        ---------

        To group them according to `handler`, instead use `sum by()`

        ```promql
        sum by (handler) (rate(prometheus_http_requests_total[5m]))
        ```

        Alternatively `sum ... without()`

        ```promql
        sum (rate(prometheus_http_requests_total[5m])) without (code)
        ```

        --------

        Filtering to only show API requires a regex match on the `handler` label, as it is not an exact match.

        The Regex string `"/api/.*"` will match any labels that begin `/api/`, this can then be added in curly brackets much the same as in exercise 2.

        ```promql
          rate(prometheus_http_requests_total{handler=~"/api/.*"}[5m])
        ```

        You should now have a graph showing a range of different activity, as using the Prometheus web interface triggers API calls in the background. 

        - `/api/v1/query_range` is probably the most frequently accessed, as completing the exercises submits calls.
        - `/api/v1/notifications/live` then has the second most calls, followed by a range of other handlers.

    <!-- Could go further and do math and comparison queries, but I don't think they add much -->



## Rules

Queries can also be used to define rules, to pre-calculate dependent metrics (recording rules), and to create alerts (alerting rules). These can be defined within separate rule `.yml` files, and will be evaluated at the frequency defined by the evaluation_interval in the main configuration file.

### Recording Rules

Recording rules, which calculate dependent metrics, can avoid the need to re-calculate expensive dependent metrics every time a dashboard is loaded.
 
The below demonstrates an example rule file which defines the record `code:prometheus_http_requests_total:sum` to sum the total number of HTTPS requests to the Prometheus server over time, for each response code.

```yml  {title="rules/http_sum.yml"}
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

```yml {title="prometheus.yml"}
rule_files: 
  - "rules/http_sum.yml" 
```

Now when Prometheus is restarted, a new metric with the name `prometheus_http_requests_total:sum_by_code` will be collected. Once the rule is first executed, it will calculate the metric for all historic data that was collected so that it can be used straight away.

!!! question "Exercise 4: Adding a recording rule"

    Create a rule which calculates a new metric, presenting the node's available memory as a percentage. You should be able to find relevant metrics output by node exporter which you configured earlier (e.g. `node_memory_...`).
    
    This will require writing a mathematical PromQL expression combining the available memory and the total memory metrics.
    
    You can check whether the rule has been loaded by visiting [http://localhost:9090/rules](http://localhost:9090/rules)

    ??? success "Solution"
    
        Create a new rules file `prometheus/rules.yml`:

        ```yml
        groups:
          - name: node_rules
            rules:
              - record: instance:node_memory_usage:percent
                expr: (1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100
        ```
        
        Update the Prometheus configuration file `prometheus/prometheus.yml` to include the rules file. If the `rule_files:` section does not already exist, it will need adding at the same indentation level as `scrape_configs`.
        
        ```yml
        rule_files:
          - "rules.yml"
          
        scrape_configs:
            ...
        ```
        
        Once these files have been created and updated, restart Prometheus.
        
        ```sh
        docker compose down
        docker compose up -d
        ```
        
        Visit [http://localhost:9090/rules](http://localhost:9090/rules), and it should now show a section `node_rules` containing the rule `instance:node_memory_usage:percent`.

### Alerting Rules

Alerting rules in Prometheus define conditions (expressed in PromQL) that, when met, cause Prometheus to fire alerts. These alerts are sent to Prometheus’ standalone Alertmanager executable, which handles grouping, deduplication, silencing, inhibition, and routing to external notification channels such as Slack, email,  Microsoft Teams or Google spaces.

Prometheus itself is responsible only for evaluating the rules and generating alert instances. Alertmanager is responsible for delivering them. (Grafana Alerting will be covered in a later chapter; the principles below still apply.)

**Core Principles for Effective Alerts**

When designing alerts, follow two fundamental principles:

1. **Alerts should not fire frequently**
   <br/>
   If an alert triggers regularly during normal operation, the team will quickly become desensitised (alert fatigue). People start ignoring or muting the channel, which defeats the purpose of alerting.
   <br/>
   Prefer thresholds and `for` durations that only fire on genuinely abnormal or sustained conditions.
2. **Alerts must be actionable**
    <br/>
    An alert should require (or at least clearly benefit from) a human response in a reasonable timeframe.
    - Good: "A node has been unreachable for 5 minutes" or "Free disk space on the shared filesystem has dropped below 10 % and is still falling."
    - Poor: "GPU utilisation reached 95 %." High utilisation during peak periods is often expected and is better reviewed later when planning capacity, not paged on.

Because both the right thresholds and the right response owners are highly environment-specific, alert conditions and notification channels should be agreed by the team that will actually receive and act on the alerts.

**Describing an Alert Rule**

Alerting rules are normally live in one or more rule files that Prometheus loads (configured via `rule_files` in `prometheus.yml`, the same as were introduced with recording rules above). A minimal but useful example of an alerting rules file looks like this:

```.yml {title="rules/node_alerts.yml"}
groups:
  - name: node
    rules:
      - alert: NodeDown
        expr: up{job="node"} == 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Node {{ $labels.instance }} is down"
          description: "{{ $labels.instance }} has been unreachable for more than 5 minutes."

      - alert: NodeHighTemp
        expr: avg by (instance, sensor) (node_hwmon_temp_celsius) > 85
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Sensor {{ $labels.sensor }} on {{ $labels.instance }} is running hot"
          description: "Temperature has been above 85 °C for 10 minutes (current value: {{ $value }} °C)."
```
<!-- Would love to include a real screenshot of how such an alert appears here, but pain setting up handlers, only managed google spaces with Grafana alerts -->

Key fields:

- `alert` – A unique name for the alert.
- `expr` – A PromQL expression that evaluates to a vector of time series. Any series that is non-empty (and satisfies the `for` clause) becomes a firing alert.
- `for` – Optional but strongly recommended. The condition must be true continuously for this long before the alert moves from "pending" to "firing". This filters out brief spikes.
- `labels` – Extra labels attached to the alert (commonly severity, team, service). These are used by Alertmanager for routing and grouping.
- `annotations` – Human-readable information. They support templating with labels (`{{ $labels.instance }}`) and the current value (`{{ $value }}`). Good annotations make the alert self-explanatory when it arrives in Slack or email.

Configuring Alertmanager is outside the scope of this course. Your team is likely to already have their alert handler configured with the appropriate user groups and services to receive notifications.

If you are asked to set up a new alert, check with your team to ensure you use appropriate labels—consistent labels help ensure alerts are handled predictably.

Your team is also likely to have documented how alerts should be handled. Make sure this documentation is updated when alerts are added or changed. You may even want to update the documentation when responding to alerts, as new information comes to light.

## Remote Writes

To enable remote writes you add a `remote_write` section to the Prometheus configuration file (`prometheus.yml`).

At its simplest the section only needs the destination URL and (usually) some form of authentication:

```yml {title="prometheus.yml"}
remote_write:
  - url: "http://long-term-storage.example.com/api/v1/write"
    basic_auth:
      username: "prometheus"
      password_file: "/etc/prometheus/secrets/password"
```

In real systems however, you almost always add extra settings. The two most common additions are:

- Queue settings: control buffering and how aggressively Prometheus sends data.
- `write_relabel_configs`: decide which metrics are sent.

Detailed tuning of the queue is outside the scope of this introductory course. However, you may be asked to adjust the relabelling rules (for example when a new exporter is added).

### Controlling what is sent (`write_relabel_configs`)

If you omit `write_relabel_configs`, every metric Prometheus has scraped is sent to the remote storage. This can be useful for full redundancy, but it is rarely desirable for long-term storage because of the volume of data.

Most sites therefore use relabelling to thin the data, keeping only the metrics that remain useful beyond the short operational window (typically days or a couple of weeks).

Every metric in Prometheus has a reserved label called `__name__` that holds the metric's name (for example `node_hwmon_temp_celsius` or `prometheus_http_requests_total` that we saw earlier). We almost always use this label when deciding what to keep or drop.

**Example: Dropping a few noisy metrics**

```yml {title="prometheus.yml#remote_write"}
write_relabel_configs:
  - source_labels: [__name__]
    regex: "go_.*"
    action: drop
```

This rule says:
“Look at the metric name (`__name__`). If it starts with `go_`, drop it. Keep everything else.”

*As covered earlier, `.*` is the Regex pattern that matches any (or no) characters.*

**Example: Dropping several groups of metrics**

```yml {title="prometheus.yml#remote_write"}
write_relabel_configs:
  - source_labels: [__name__]
    regex: "go_.*|prometheus_.*"
    action: drop
```

The `|` character means "or", so this single rule drops any metric with a name that starts with `go_` or `prometheus_`.

You will normally only need the two actions `drop` and `keep` at this stage.

`keep` works in the opposite way to `drop`, it discards metrics that do not match the Regex pattern and only keeps the ones that do.

Relabelling rules are applied in the order they are written. This means the order matters when you combine rules, a metric that is dropped by an earlier rule will never reach later rules.

More advanced label manipulation exists, but it is outside the scope of this introductory course.
