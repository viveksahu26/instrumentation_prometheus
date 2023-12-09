Prom-AlertManager Setup

The job of prometheus is to scrape metrics(information) from endpoints. At those particular endpoints some kind of metrics is exposed by programs running to that corresponding endpoints or ports. Those program uses prometheus client to setup instrumental code which basically tells program what metrics to expose. 

For example: A simple golang web server program adds the instrumental code for the `ping_request_count` metrics. This metric simply counts the request being made to the web server program. 

Instrumental code:
```go
    var pingCounter = prometheus.NewCounter(
        prometheus.CounterOpts{
            Name: "ping_request_count",
            Help: "No of request handled by Ping handler",
        },
    )
```

The application calculates the request count by incrementing the value of pingCounter by 1 for each time this function is revoked or called internally.

```go
    func ping(w http.ResponseWriter, _ *http.Request) {
        pingCounter.Inc()
        fmt.Fprintf(w, "pong")
    }
```

 After program succesfully getting the request it exposes to "/metrics" endpoint.

 ```go
    http.Handle("/metrics", promhttp.Handler())
 ```

And since the Prometheus program was configured to continuolsy watch that "/metric" endpoint at a specific time or particular time. So, it simply scrapes the count metric because it was configured to watch that endpoint or port.

But how prometheus knows what all enpoints to watch at ?
It gets from `prometheus.yml` file. 
cat `prometheus.yaml`

```yaml
    global:
        scrape_interval: 15s
        evaluation_interval: 10s
    scrape_configs:
    - job_name: prometheus
        static_configs:
        - targets: ["localhost:9090"]
    - job_name: simple_server
        static_configs:
        - targets: ["localhost:8090"]
```

