---
title: ShedLoad
---
# `ShedLoad` topic

This instruction requests the node to reduce the power of a device managed by a specific control by
a specific amount. The actual meaning of _reduce_ is flexible, in that in some systems a "reduction"
can be achieved via the extra _supply_ of the requested power.

=== "ShedLoad example"

	```json
	{
		"nodeId": 123,
		"topic": "ShedLoad",
		"parameters": [
			{"name": "/power/1", "value": "1000"}
		]
	}
	```

=== "ShedLoad example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "ShedLoad",
		"params": {
			"/power/1": "1000"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `{controlId}` | The parameter name is the ID of the control to handle the instruction. The value should be the number of **watts** to reduce the power by. If a value of `0` is requested, then stop actively managing the load. |
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |

## Result

A `Completed` state indicates the load shed amount was successfully applied on the control.
