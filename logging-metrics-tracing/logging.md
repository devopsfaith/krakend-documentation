---
lastmod: 2018-10-30
date: 2018-10-30
toc: true
linktitle: Logging
title: Logging - Syslog, stdout and GELF
weight: 10
menu:
  documentation:
    parent: logging-metrics-tracing
---
When the service starts, if no logging configuration is set, all the log events are sent to the **standard output** using the basic logger capabilities of the KrakenD framework. The reporting level, in that case, is `DEBUG` and adds no prefix to the logs.

# Extending the logging capabilities
Different logging components allow you to extend the logging functionality, such as sending the events to the **syslog**, choosing the verbosity level or use the **Graylog Extended Log Format (GELF)**.

## Improved logging with `gologging`.
The component `gologging` extends the default logging capabilities with the following capabilities:

- Option to write to the stdout
- Option to write to the syslog
- Add a prefix to log lines
- Select the reporting level

## Enabling `gologging`

To enjoy the extended logging capabilitites the component needs to be added in the `krakend.json` configuration. Add its namespace in the `extra_config` at the root level:

    {
      "version": 2,
      "extra_config": {
        "github_com/devopsfaith/krakend-gologging": {
          "level": "INFO",
          "prefix": "[KRAKEND]",
          "syslog": true,
          "stdout": true
        }
      }

The snippet above shows the four options you can configure, explained below.

### Set the *reporting level*
Define the severity you would like to see in the logs with `level`. The recognized options from more to less verbosity are:

- `DEBUG`
- `INFO`
- `WARNING`
- `ERROR`
- `CRITICAL`

### Write to Syslog or Stdout
For each log event triggered, the output can write to the **syslog**, the **stdout**, or both. The accepted values are a boolean. When true, the log writes in the selected target:

- `"syslog": true`
- `"stdout": true`

### Add a prefix to all lines
Besides, you might want to choose to add a string to every logged line, so you can quickly filter messages with external tools later.

- `"prefix": "[ANY STRING]"`

# Graylog and the GELF format
KrakenD supports sending structured events in GELF format to your Graylog Cluster (KrakenD 0.7+) thanks to the [krakend-gelf](https://github.com/devopsfaith/krakend-gelf) integration.

The setup of GELF is straightforward and requires only to set two parameters:

- `address`: The address (including the port) of your Graylog cluster (or any other service that receives GELF inputs).
- `enable_tcp`: Set to `false` (recommended) to use UDP. When using TCP performance might be affected.

## Enabling GELF
Add the `krakend-gelf` integration in the root level of your `krakend.json`, inside the `extra_config` section. **The `gologging` needs to be enabled too**.

For instance:

    "extra_config": {
      "github_com/devopsfaith/krakend-gelf": {
        "address": "myGraylogInstance:12201",
        "enable_tcp": false
      }
      "github_com/devopsfaith/krakend-gologging": {
          "level": "INFO",
          "prefix": "[KRAKEND]",
          "syslog": false,
          "stdout": true
      }
    }