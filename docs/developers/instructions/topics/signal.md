---
title: Signal
---
# `Signal` topic

This instruction provides a way to send a named _signal_ to a specific control. Signal names are
defined by the control that supports this instruction, so consult the documentation for the
control you are interested in signaling. For example a camera control might support a `snapshot`
signal that triggers the control to capture an image from the attached camera.

=== "Signal example"

	```json
	{
		"nodeId": 123,
		"topic": "Signal",
		"parameters": [
			{"name": "/camera/1", "value": "snapshot"}
		]
	}
	```

=== "Signal example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "Signal",
		"params": {
			"/power/1": "1000"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `{controlId}` | The parameter name is the ID of the control to signal. Typically this is the same as the _source ID_ if a device. The value should be a the name of the signal to send. |
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |

## Result

A `Completed` state indicates the load shed amount was successfully applied on the control.
