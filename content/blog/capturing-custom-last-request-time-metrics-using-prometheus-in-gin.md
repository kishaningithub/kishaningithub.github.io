+++
title = "Capturing Custom Last Request Time Metrics Using Prometheus in Gin"
description = ""
date = 2021-09-17T14:15:41+05:30
tags = ["prometheus", "gin", "go", "monitoring"]
categories = []
+++

We will be using [golang](https://golang.org/), [gin](https://github.com/gin-gonic/gin), [prometheus go library](https://github.com/prometheus/client_golang) to expose last request time metrics 

## Out of the box metrics

Spin up a gin go application which exposes the metrics which comes out of the box via the /metrics endpoint

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/prometheus/client_golang/prometheus/promhttp"
	"log"
)

func main() {
	r := gin.Default()
	r.GET("/metrics", func(c *gin.Context) {
		handler := promhttp.Handler()
		handler.ServeHTTP(c.Writer, c.Request)
	})
	err := r.Run(":8080")
	if err != nil {
		log.Fatalln(err)
	}
}
```

Run the application using command `go run main.go` and
When you hit the `/metrics` you will see the following

```shell
$ curl http://localhost:8080/metrics

# HELP go_gc_duration_seconds A summary of the pause duration of garbage collection cycles.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 0
go_gc_duration_seconds{quantile="0.25"} 0
go_gc_duration_seconds{quantile="0.5"} 0
go_gc_duration_seconds{quantile="0.75"} 0
go_gc_duration_seconds{quantile="1"} 0
go_gc_duration_seconds_sum 0
go_gc_duration_seconds_count 0
# HELP go_threads Number of OS threads created.
# TYPE go_threads gauge
go_threads 7
# HELP promhttp_metric_handler_requests_in_flight Current number of scrapes being served.
# TYPE promhttp_metric_handler_requests_in_flight gauge
promhttp_metric_handler_requests_in_flight 1
```
(Truncated the above for brevity)

## Custom last_request_received_time metric

Now let us expose our custom metric which is the last request time stamp

Going with [gauge data type](https://prometheus.io/docs/concepts/metric_types/#gauge) as for this use case we are "setting" the value as a timestamp

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/prometheus/client_golang/prometheus"
	"github.com/prometheus/client_golang/prometheus/promhttp"
	"log"
)

func main() {
	r := gin.Default()

	// Gauge registration
	lastRequestReceivedTime := prometheus.NewGauge(prometheus.GaugeOpts{
		Name: "last_request_received_time",
		Help: "Time when the last request was processed",
	})
	err := prometheus.Register(lastRequestReceivedTime)
	handleErr(err)

	// Middleware to set lastRequestReceivedTime for all requests
	r.Use(func(context *gin.Context) {
		lastRequestReceivedTime.SetToCurrentTime()
	})

	// Metrics handler
	r.GET("/metrics", func(c *gin.Context) {
		handler := promhttp.Handler()
		handler.ServeHTTP(c.Writer, c.Request)
	})
	err = r.Run(":8080")
	handleErr(err)
}

func handleErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}
```

So now when you hit the metrics endpoint you will observe the newly created `last_request_received_time` metric. 

```shell
$ curl http://localhost:8080/metrics

# HELP last_request_received_time Time when the last request was processed
# TYPE last_request_received_time gauge
last_request_received_time 1.63186694449664e+09
```
(removed the other metrics for brevity)

## Custom last_request_received_time with dynamic labels

Now say we want to categorize this based on HTTP headers(Eg userId, Operation type). What this means is the label names are constant but the values are different. 
For this case we have the [GaugeVec](https://pkg.go.dev/github.com/prometheus/client_golang/prometheus#GaugeVec) type in the library

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/prometheus/client_golang/prometheus"
	"github.com/prometheus/client_golang/prometheus/promhttp"
	"log"
	"net/http"
)

var (
	HeaderUserId        = "user_id"
	HeaderOperationType = "operation_type"
)

func main() {
	r := gin.Default()

	// Gauge registration
	lastRequestReceivedTime := prometheus.NewGaugeVec(prometheus.GaugeOpts{
		Name: "last_request_received_time",
		Help: "Time when the last request was processed",
	}, []string{HeaderUserId, HeaderOperationType})
	err := prometheus.Register(lastRequestReceivedTime)
	handleErr(err)

	// Metrics handler
	r.GET("/metrics", func(c *gin.Context) {
		handler := promhttp.Handler()
		handler.ServeHTTP(c.Writer, c.Request)
	})

	// Middleware to set lastRequestReceivedTime for all requests
	middleware := func(context *gin.Context) {
		lastRequestReceivedTime.With(prometheus.Labels{
			HeaderUserId:        context.GetHeader(HeaderUserId),
			HeaderOperationType: context.GetHeader(HeaderOperationType),
		}).SetToCurrentTime()
	}

	// Request handler
	r.GET("/data", middleware, func(c *gin.Context) {
		c.JSON(http.StatusOK, map[string]string{"status": "success"})
	})

	err = r.Run(":8080")
	handleErr(err)
}

func handleErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}

```

```shell
$ curl -H "user_id: user1" -H "operation_type: fetch" http://localhost:8080/data
{"status":"success"}

$ curl -H "user_id: user1" -H "operation_type: view" http://localhost:8080/data
{"status":"success"}

$ curl -H "user_id: user1" -H "operation_type: upload" http://localhost:8080/data
{"status":"success"}

$ curl http://localhost:8080/metrics

# HELP last_request_received_time Time when the last request was processed
# TYPE last_request_received_time gauge
last_request_received_time{operation_type="fetch",user_id="user1"} 1.6318757060797539e+09
last_request_received_time{operation_type="upload",user_id="user1"} 1.631875691071805e+09
last_request_received_time{operation_type="view",user_id="user1"} 1.631875931726781e+09
```

As we see above for the same metric we have different valued labels

The code for this is available at
https://github.com/kishaningithub/lastrequesttimemetrics

Feedback is welcome :-)

### References
- https://stackoverflow.com/questions/65608610/how-to-use-gin-as-a-server-to-write-prometheus-exporter-metrics
- https://prometheus.io/docs/guides/go-application/
- https://pkg.go.dev/github.com/prometheus/client_golang/prometheus#GaugeVec