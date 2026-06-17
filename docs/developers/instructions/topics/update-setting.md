---
title: UpdateSetting
---
# `UpdateSetting` topic

This instruction requests the node to update a configurable runtime
[setting](../../../users/settings.md) value. If the setting does not exist, it will be added.

=== "UpdateSetting example"

	```json
	{
		"nodeId": 123,
		"topic": "UpdateSetting",
		"parameters": [
			{"name": "key", "value": "k1"},
			{"name": "type", "value": "t1"},
			{"name": "value", "value": "newvalue"}
		]
	}
	```

=== "UpdateSetting example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "UpdateSetting",
		"params": {
			"key": "k1",
			"type": "t1",
			"value": "newvalue"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `key` | The Settig key. |
| `type` | The Settig type. |
| `value` | The Settig value. |
| `flags` | The optional Settig flags. |

## Result

A `Completed` state indicates the node will update the platform.

[s3-setup]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.setup.s3
