# snapshot_grafana

CLI tool to take snapshots of grafana dashboards

Currently supports only the Prometheus datasource.

# Warning

This tool is very much in development and is likely to change considerably.

# Todo

* ~~Refactor and break out into lib~~( The refactoring still needs to happen)
* ~~find out why timeshifted stuff doesn't work~~ (more of an issue with null "snapshotData" fields)
* ~~make it usable as a cli tool~~ (barely)
* ~~document~~ (some)
* ~~Dockerise~~ (alexrudd/snapshot_grafana)
* snapshot annotations
* async the datasource reqs
* "Take()" should take a context
* use a json library instead of all that casting
* support other datasources
* builds

# Usage

## As Lib:

```go
package main

import (
	"fmt"
	"github.com/alexrudd/snapshot_grafana/snapshot"
	"net/url"
	"time"
)

func main() {
	grafanaURL, _ := url.Parse("http://grafana.myorg.com/")
	from := time.Date(2017, time.February, 05, 6, 0, 0, 0, time.Local)
	to := time.Date(2017, time.February, 05, 12, 0, 0, 0, time.Local)

	config := &snapshot.Config{
		// The domain and path where your Grafana instance is located
		GrafanaAddr: grafanaURL,
		// An api key which has admin privelages and access to the target dashboard
		GrafanaAPIKey: "eyJrIjoib3M0RDRWNmxYbnQ3bEJKNVUwOFE1Rk0wZnFrRXR3eDEiLCJuIjoia2V5IiwiaWQiOjN9",
	}
	snapclient, _ := snapshot.NewSnapClient(config)

	takeConfig := &snapshot.TakeConfig{
		DashSlug: "my-dash-slug",
		From:     &from,
		To:       &to,
	}
	snapshot, err := snapclient.Take(takeConfig)
	if err != nil {
		fmt.Println(err.Error())
		return
	}
	fmt.Println(snapshot.URL)
}

```

## As cli

```sh
go get github.com/alexrudd/snapshot_grafana
snapshot_grafana \
  -grafana_addr="http://grafana.myorg.com/" \
  -grafana_api_key="eyJrIjoib3M0RDRWNmxYbnQ3bEJKNVUwOFE1Rk0wZnFrRXR3eDEiLCJuIjoia2V5IiwiaWQiOjN9" \
  -dashboard_slug="my-dash-slug"
```

Or using Docker:

```sh
docker run --rm alexrudd/snapshot_grafana \
  -grafana_addr="http://grafana.myorg.com/" \
  -grafana_api_key="eyJrIjoib3M0RDRWNmxYbnQ3bEJKNVUwOFE1Rk0wZnFrRXR3eDEiLCJuIjoia2V5IiwiaWQiOjN9" \
  -dashboard_slug="my-dash-slug"
```
