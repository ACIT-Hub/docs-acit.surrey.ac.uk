# Prometheus Docker Exercise Notes

<!-- Running inside WSL, docker commands all required sudo, will this be a problem? -->

## Setup

`docker compose up -d` will initialise and run the training environment.

- Prometheus running at http://localhost:9090/
- Grafana running at http://localhost:3000/
    - Username and password are both `learning`
    - Prometheus has already been configured
- Node exporter is running at http://localhost:9100/
    - This has not been added to Prometheus *(That will be the first exercise).

Check that each of these pages are reachable!

If Prometheus or Grafana require restarting, you can shutdown and restart the container.

```sh
docker compose down
docker compose up -d
```

Also remember to shutdown the container when you've finished!

## Exercise 1 - Prometheus: Adding a new scrape target

Update `prometheus/prometheus.yml` to read from node exporter which can be accessed by Prometheus at `node-exporter:9100`.

As explained earlier, add a label `acit` to the node exporter scrape target and override it's scrape interval to be one second.

*Once node exporter has been added as a scrape target to Prometheus, it will take time for Prometheus to gather metrics from the new source. To quickly check whether it has been configured correctly, visit http://localhost:9090/targets where it should be listed with the name you gave it.*

<details>
<summary>Prometheus not running?</summary>
When making changes to Prometheus' configuration, it's possible to introduce a syntax error which will prevent it from starting.

If Prometheus doesn't appear to be running, try checking the output logs which might highlight a syntax error.

```sh
docker compose logs prometheus
```

<details>


**Ensure you leave docker running, so that metrics can be collected for the following exercises!**

<details>
<summary>Solution</summary>
  
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

If working correctly, http://localhost:9090/targets should now contain a box titles "node_exporter" which will be shown as "up" in the top right corner.
  
</details>


<!-- We will want to have a long break between setup and working with prometheus/grafana (unless we can prep prometheus with synthetic data somehow, or have them setup a different scrape target as setup can be done right at the beginning) -->

## Exercise 2 - Prometheus: Querying

In this exercise you will explore PromQL

Enter the following query, what does this show?

### A) Instant Vector Query

```promql
up
```

<details>
<summary>Solution</summary>
  
In table view, it will show a table with 2 rows, representing a metric for the two scrape jobs; `prometheus` the default exporter, and `node-exporter` that you setup during the previous exercise.

The table shows their instant value, which are hopefully both `1` as they should be running.
</details>

### B) Filter Query

Amend the previous query so that it only returns the `up` metric for your `node-exporter`.


<details>
<summary>Solution</summary>
You can filter by specifying any of the labels unique to `node-exporter` inside curly bracket, e.g.

```promql
up{job="node-exporter"}
```

or 

```promql
up{instance="node-exporter:9100"}
```

</details>


### C) Range Vector Query

Enter the following query, and select the "graph" tab to visualise the results.

```promql
rate(prometheus_http_requests_total[5m])
```
Try increasing and decreasing the time range.

What does this show?

<details>
<summary>Solution</summary>
`prometheus_http_requests_total` is an instant vector metric, it acts as a counter of the total number of page visits labelled according to the accessed URL (`handler`) and the http response code (`code`).

The `rate()` function calculates the rate of increase with a sliding window equal to the specified time-range. Therefore, decreasing the time-range (e.g. `[1m]`) will make the graph more spiky, as each visit will affect less sliding windows, and increasing it will have the opposite effect.

<!-- I don't really like this, but feels like we should talk about selecting an appropriate time-range. -->
The "best" time-range to use, would depend on the use-case and deviation, although a lower value is more representative of the truth.

</details>

### D) Aggregation

Update your query, to show the combined sum of all `prometheus_http_requests_total` metrics over time, to produce a graph with a single line representing page views.

Update this query to group requests according to `handler`, ignoring `code`, to more clearly identify the most accessed pages at any time.

Now extend it once more, to filter so that only API calls are returned.


<details>
<summary>Hint</summary>

`.` is the regex symbol which will match any character.
`*` is the regex symbol which requires the previous element of the regex pattern to be matched 0 or more times.

So a suitable regex string to match only `handler` labels for API calls would be `/api/.*`.

</details>

Which API call dominates http requests? Which is second?

<details>
<summary>Solution</summary>
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

The regex string `"/api/.*"` will match any labels that begin `/api/`, this can then be added in curly brackets much the same as in exercise 2.

```promql
  rate(prometheus_http_requests_total{handler=~"/api/.*"}[5m])
```

You should now have a graph showing a range of different activity, as using the Prometheus web interface triggers API calls in the background. 

- `/api/v1/query_range` is probably the most frequently accessed, as completing the exercises submits calls.
- `/api/v1/notifications/live` then has the second most calls, followed by a range of other handlers.

</details>

<!-- Could go further and do math and comparisons, but I don't think they add much -->
