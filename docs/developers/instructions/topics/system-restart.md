---
title: SystemRestart
---
# `SystemRestart` topic

This instruction requests the node to restart the SolarNode service.

=== "SystemRestart example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemRestart"
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |

## Result

A `Completed` state indicates SolarNode will restart.
