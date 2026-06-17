---
title: LoggingSetLevel
---
# `LoggingSetLevel` topic

This instruction updates a node's logging verbosity by adjusting the level of one or more loggers.
See [Logging](../../../users/logging.md) for information about how SolarNode logging works.

=== "LoggingSetLevel example"

	```json
	{
		"nodeId": 123,
		"topic": "LoggingSetLevel",
		"parameters": [
			{
				"name": "logger",
				"value": "net.solarnetwork.node.io.modbus,net.solarnetwork.node.datum.modbus"
			},
			{
				"name": "level",
				"value": "trace"
			}
		]
	}
	```

=== "LoggingSetLevel example (compact)"

	```json
	{
		"nodeId": 123,
		"params": {
			"logger": "net.solarnetwork.node.io.modbus,net.solarnetwork.node.datum.modbus",
			"level": "trace"
		}
	}
	```


## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `level` | The logging verbosity. See [below](#supported-levels) for more information. |
| `logger` | A comma-delimited list of logger names to update. More than one of this parameter can be provided; all loggers provided will be adjusted. |

### Supported levels

The following level values are supported for the `level` instruction parameter:

| Level | Description |
| :-- | :-- |
| `inherit` | Inherit the level from the logger's parent hierarchy. |
| `trace` | The highest level of verbosity for tracing messages. Includes all other levels. |
| `debug` | The 2nd highest level of verbosity for debugging messages. Includes `info`, `warn`, and `error` levels. |
| `info` | A "default" level of verbosity for informational messages. Includes `warn` and `error` levels. |
| `warn` | The 2nd most urgent level of verbosity for potential problems. Includes the `error` level. |
| `error` | The most urgent level of verbosity for error conditions. |
| `off` | No logging at all. |

## Result

A `Completed` state indicates the instructions where executed successfully, according to the
`successMode` and `failFast` parameter logic described in the previous section.
