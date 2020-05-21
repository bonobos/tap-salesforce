# tap-salesforce

[![PyPI version](https://badge.fury.io/py/tap-mysql.svg)](https://badge.fury.io/py/tap-salesforce)
[![CircleCI Build Status](https://circleci.com/gh/singer-io/tap-salesforce.png)](https://circleci.com/gh/singer-io/tap-salesforce.png)


[Singer](https://www.singer.io/) tap that extracts data from a [Salesforce](https://www.salesforce.com/) database and produces JSON-formatted data following the [Singer spec](https://github.com/singer-io/getting-started/blob/master/docs/SPEC.md).

```bash
$ mkvirtualenv -p python3 tap-salesforce
$ pip install tap-salesforce
$ tap-salesforce --config config.json --discover
$ tap-salesforce --config config.json --properties properties.json --state state.json
```

# Quickstart

## Install the tap

```
> pip install tap-salesforce
```

## Create a Config file

```
{
  "client_id": "secret_client_id",
  "client_secret": "secret_client_secret",
  "refresh_token": "abc123",
  "start_date": "2017-11-02T00:00:00Z",
  "api_type": "BULK",
  "select_fields_by_default": true
}
```

The `client_id` and `client_secret` keys are your OAuth Salesforce App secrets. The `refresh_token` is a secret created during the OAuth flow. For more info on the Salesforce OAuth flow, visit the [Salesforce documentation](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).

The `start_date` is used by the tap as a bound on SOQL queries when searching for records.  This should be an [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) formatted date-time, like "2018-01-08T00:00:00Z". For more details, see the [Singer best practices for dates](https://github.com/singer-io/getting-started/blob/master/BEST_PRACTICES.md#dates).

The `api_type` is used to switch the behavior of the tap between using Salesforce's "REST" and "BULK" APIs. When new fields are discovered in Salesforce objects, the `select_fields_by_default` key describes whether or not the tap will select those fields by default.

## Run Discovery

To run discovery mode, execute the tap with the config file.

```
> tap-salesforce --config config.json --discover > catalog.json
```

## Sync Data

To sync data, select fields in the `properties.json` output and run the tap.

```
> tap-salesforce --config config.json --catalog catalog.json [--state state.json]
```

## Catalog, Extraction and Replication Types

The included catalog file, `catalog.json`, is constructed to extract the Contact API and perform INCREMENTAL sync. Refer
to the [Singer spec](https://github.com/singer-io/getting-started/blob/master/docs/SYNC_MODE.md#replication-method) for more information about replication types

By default, discovery mode will produce a catalog with forces FULL_TABLE sync for all streams. 

To enable INCREMENTAL:

### Determine the replication key for the stream. 

The possible list of replication keys are normally identified by discovery mode. These will be written to the stream 
metadata in the catalog in `valid-replication-keys`.

```json
{
  "breadcrumb": [],
  "metadata": {
    "valid-replication-keys": [
      "SystemModstamp"
    ],
    "table-key-properties": [
      "Id"
    ],
    "selected": true
  }
}
```

### Add replication key to the catalog

Edit stream metadata in the catalog to identify the desired replication key.

```json
{
  "breadcrumb": [],
  "metadata": {
    "replication-key": "SystemModstamp",
    "valid-replication-keys": [
      "SystemModstamp"
    ],
    "table-key-properties": [
      "Id"
    ],
    "selected": true
  }
}
```



Copyright &copy; 2017 Stitch
