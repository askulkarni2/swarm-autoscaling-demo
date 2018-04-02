# Orbiter Demo

This stack demonstrates the use of [orbiter](https://github.com/gianarb/orbiter) to scale services up/down on a docker swarm. The stack consists of,
* [orbiter](https://github.com/gianarb/orbiter) - Swarm Service Scaler
* [Docker HAProxy](https://hub.docker.com/r/dockercloud/haproxy) - Load Balancer
* [Docker Hello World](https://hub.docker.com/r/dockercloud/hello-world) - Sample App
* [Prometheus](https://prometheus.io) - Collect Metrics
* [HAProxy Exporter](https://github.com/prometheus/haproxy_exporter) - Provide HA Proxy metric endpoint
* [AlertManager](https://prometheus.io/docs/alerting/overview) - Alert Processing
* [Unsee](https://github.com/cloudflare/unsee) - Alert Dashboard
* [Grafana](https://grafana.com) - Metric Dashboard

## Install
### Prerequisites
* [Docker Version 18.02.0-ce](https://docs.docker.com/install/)

Clone this repository and run the stack:
```
git clone https://github.com/askulkarni2/swarm-autoscaling-demo.git
docker stack deploy -c docker-compose.yml orbiterDemo
```

## Simulation Test
### Prerequisites
* [Vegeta](https://github.com/tsenart/vegeta)

`./vegeta.sh`

The sample load is generated by `vegeta.sh`. The script exerts load on the HA proxy in increments of 25 requests per second upto 150 and the decrements the load in decrements of 25. The central assumption of this simulation test is that one task of the `app` service can serve 25 requests with a 100% HTTP 200 response at an acceptable mean latency of ~ 4 ms. Given this we would expect that the number of tasks be as follows

| RPS | Tasks  |
| ----|:------:|
| 25  | 1(min) |
| 50  | 1      |
| 75  | 2      |
| 100 | 2      |
| 125 | 3      |
| 150 | 3      |
| 125 | 3      |
| 100 | 2      |
| 75  | 2      |
| 50  | 1      |


Without much tuning of the prometheus configuration the actual grafana plot for #desired vs #actual is as follows:

![Grafana](https://raw.githubusercontent.com/askulkarni2/swarm-autoscaling-demo/master/images/grafana.png)
