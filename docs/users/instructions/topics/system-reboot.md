---
title: SystemReboot
---
# `SystemReboot` topic

This instruction requests the node to reboot the device SolarNode is running on.

=== "SystemReboot example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemReboot"
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |

## Result

A `Completed` state indicates the node will reboot.
