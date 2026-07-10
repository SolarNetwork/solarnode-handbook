---
title: CancelInstruction
---
# `CancelInstruction` topic

This instruction asks a node to cancel a previously-received instruction, by changing its state from
`Received` to `Declined`. This is designed to work with [deferred instructions](../index.md#deferred-instructions).

=== "CancelInstruction example"

	```json
	{
		"nodeId": 123,
		"topic": "CancelInstruction",
		"parameters": [
			{"name": "id", "value": "12345"}
		]
	}
	```

=== "CancelInstruction example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "CancelInstruction",
		"params": {
			"id": "12345"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `id` | The `id` of the instruction to cancel. |

## Result

A `Completed` state indicates the target instruction was cancelled. A `Declined` state
indicates the target instruction was not cancelled.
