# CrateDB Cloud API Client Go Library

The CrateDB Cloud API client Go library to allow programmatic access to the Cloud products.

## Generated types and API client

This library is generated using [oapi-codegen](https://github.com/oapi-codegen/oapi-codegen) from this [OpenAPI spec](https://console.cratedb.cloud/api/cloud-api-openapi-v1.0.0.json)

### Generate

```go
go generate ./...
```

## Usage

### Environment variables

```bash
export CRATEDB_BASE_URL="https://console.cratedb.cloud/"
export CRATEDB_API_KEY="crate_09NZV-SXkpX3feMJWXxnSNY2AAa98RlKkxqvqdQBlfC"
export CRATEDB_API_SECRET="S5ChS_eQHoUxzplSOv11xQrFRD8W_G-I5Z43w56RIqn"
```

### Sample code to list database tokens

```go
package main

import (
	"context"
	"io"
	"net/http"

	"github.com/caarlos0/env/v11"
	"github.com/thulasirajkomminar/cratedb"
)

type CratedbConfig struct {
	BaseURL   string `env:"CRATEDB_BASE_URL"`
	ApiKey    string `env:"CRATEDB_API_KEY"`
	ApiSecret string `env:"CRATEDB_API_SECRET"`
}

func main() {
	cfg := CratedbConfig{}
	opts := env.Options{RequiredIfNoDef: true}

	err := env.ParseWithOptions(&cfg, opts)
	if err != nil {
		panic(err)
	}

	ctx := context.Background()
	client, err := cratedb.NewClient(cfg.BaseURL, cratedb.WithRequestEditorFn(func(ctx context.Context, req *http.Request) error {
		req.SetBasicAuth(cfg.ApiKey, cfg.ApiSecret)
		return nil
	}))
	if err != nil {
		panic(err)
	}

	resp, err := client.GetApiV2UsersMe(ctx)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	if resp.StatusCode == http.StatusOK {
		bodyBytes, err := io.ReadAll(resp.Body)
		if err != nil {
			panic(err)
		}
		bodyString := string(bodyBytes)
		println(bodyString)
	}
}
```
