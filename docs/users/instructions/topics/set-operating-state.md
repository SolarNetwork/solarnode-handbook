---
title: SetOperatingState
---
# `SetOperatingState` topic

This instruction requests the node to update the [operating state][opstates] of a device. For
example, this can be used to turn a device on or off.

=== "SetOperatingState example"

	```json
	{
		"nodeId": 123,
		"topic": "SetOperatingState",
		"parameters": [
			{"name": "/inverter/1", "value": "Shutdown"}
		]
	}
	```

=== "SetOperatingState example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "SetOperatingState",
		"params": {
			"/inverter/1": "Shutdown"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `{controlId}` | The **control ID** of the device whose state should be updated, with the associated parameter value holding the desired [operating state][opstates] to set the device state to. |
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |

## Result

A `Completed` state indicates the operating state was successfully updated on the device.

[opstates]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#standard-device-operating-states
