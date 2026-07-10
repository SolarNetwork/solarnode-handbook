---
title: EnableOperationalModes
---
# `EnableOperationalModes` topic

This instruction asks a node to **activate** one or more [operational modes](../../../users/op-modes.md).
See the [`DisableOperationalModes`](./disable-operational-modes.md) topic to deactivate modes.

=== "EnableOperationalModes example"

	```json
	{
		"nodeId": 123,
		"topic": "EnableOperationalModes",
		"parameters": [
			{"name": "OpMode", "value": "inverters-off"}
		]
	}
	```

=== "EnableOperationalModes example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "EnableOperationalModes",
		"params": {
			"OpMode": "inverters-off"
		}
	}
	```

=== "EnableOperationalModes with expiration example"

	```json
	{
		"nodeId": 123,
		"topic": "EnableOperationalModes",
		"parameters": [
			{"name": "OpMode", "value": "inverters-off"},
			{"name": "Expiration", "value": "1564617600000"}
		]
	}
	```


## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `Expiration` | An _optional_ date at which the modes should automatically be disabled, as milliseconds since the Unix epoch. If not provided the modes will remain active until deactivated via a [`DisableOperationalModes`](./disable-operational-modes.md) instruction. If an operational mode is already active, this value will **replace** any existing expiration date for that mode. |
| `OpMode` | The mode to activate. Multiple `OpMode` parameters can be provided (in the non-compact form) to activate multiple modes. |

## Result

A `Completed` state indicates the operational mode(s) were deactivated.
