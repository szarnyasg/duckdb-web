---
layout: docu
title: Troubleshooting
---

## Curl Request Fails

### Problem

When trying to attach to an Iceberg REST Catalog endpoint, DuckDB returns the following error:

```console
IO Error:
Curl Request to '/v1/oauth/tokens' failed with error: 'URL using bad/illegal format or missing URL'
```

### Solution

Make sure that you have the latest Iceberg extension installed:

```bash
duckdb
```

```plsql
FORCE INSTALL iceberg FROM core_nightly;
```

Exit DuckDB and start a new session:

```bash
duckdb
```

```plsql
LOAD iceberg;
```
