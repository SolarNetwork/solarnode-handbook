---
title: DisableOperationalModes
---
# `DisableOperationalModes` topic

This instruction asks a node to **deactivate** one or more [operational modes](../../../users/op-modes.md).
See the [`EnableOperationalModes`](./enable-operational-modes.md) topic to activate modes.

=== "DisableOperationalModes example"

	```json
	{
		"nodeId": 123,
		"topic": "DisableOperationalModes",
		"parameters": [
			{"name": "OpMode", "value": "inverters-off"}
		]
	}
	```

=== "DisableOperationalModes example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "DisableOperationalModes",
		"params": {
			"OpMode": "inverters-off"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `OpMode` | The mode to deactivate. Multiple `OpMode` parameters can be provided (in the non-compact form) to deactivate multiple modes. |

## Result

A `Completed` state indicates the operational mode(s) were deactivated.
