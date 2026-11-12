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

## Exercise 1

Update `prometheus/prometheus.yml` to read from node exporter which can be accessed by Prometheus at `node-exporter:9100`.

As explained earlier, add a label `acit` to the node exporter scrape target and override it's scrape interval to be one second.

*Once node exporter has been added as a scrape target to Prometheus, it will take time for Prometheus to gather metrics from the new source. To quickly check whether it has been configured correctly, visit http://localhost:9090/targets where it should be listed with the name you gave it.*

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


<!-- We will want to have a long break between setup and working with prometheus/grafana (unless we can prep prometheus with synthetic data somehow) -->

