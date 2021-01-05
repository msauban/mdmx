[![Build Status](https://travis-ci.org/sesam-community/mdmx.svg?branch=master)](https://travis-ci.org/sesam-community/mdmx)
Sesam.io sink to MDMx

This MS convert array of element(s) [{}] into a single element {} as required by the MDMx api.

Example System Config

```json
{
  "_id": "mdmx",
  "type": "system:microservice",
  "docker": {
    "environment": {
      "BASE_URL": "https://mdmx.dev-elvia.io/api", <- MDMx api URL
      "CLIENT-ID": "$ENV(mdmx-client-id)",
      "CLIENT-SECRET": "$SECRET(mdmx-client-secret)",
      "GRANT_TYPE": "client_credentials",
      "TOKEN_URL": "https://elvid.test-elvia.io/connect/token"
    },
    "image": "sesamcommunity/mdmx:1.1.1",
    "port": 5000
  },
  "verify_ssl": true
}
```

Example endpoint pipe config

```json
{
  "_id": "meterpoint-mdmx-endpoint",
  "type": "pipe",
  "source": {
    "type": "dataset",
    "dataset": "meterpoint-mdmx"
  },
  "sink": {
    "type": "json",
    "system": "mdmx",
    "url": "/meteringpoints"
  },
  "transform": {
    "type": "dtl",
    "rules": {
      "default": [
        ["filter",
          ["not", "_S._deleted"]
        ],
        ["copy", "*", "_*"]
      ]
    }
  },
  "pump": {
    "log_events_noop_runs": true,
    "log_events_noop_runs_changes_only": false,
    "max_consecutive_write_errors": 50,
    "max_read_retries": 3,
    "max_retries_per_entity": 120,
    "max_write_errors_in_retry_dataset": 1000,
    "read_retry_delay": 5,
    "schedule_interval": 300
  },
  "batch_size": 1,
  "remove_namespaces": true
}
```