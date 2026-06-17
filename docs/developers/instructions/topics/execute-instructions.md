---
title: ExecuteInstructions
---
# `ExecuteInstructions` topic

This instruction allows multiple arbitrary instructions to be executed as a group.

=== "ExecuteInstructions example"

	```json
	{
		"nodeId": 123,
		"topic": "ExecuteInstructions",
		"parameters": [
			{
				"name": "instructions",
				"value": "[{\"topic\":\"SetControlParameter\",\"params\":{\"/battery/rate\":\"-1500\"}},{\"topic\":\"SetControlParameter\",\"params\":{\"/grid/target\":\"-1250\"}}]"
			}
		]
	}
	```

=== "ExecuteInstructions example (compact)"

	```json
	{
		"nodeId": 123,
		"params": {
			"instructions": "[{\"topic\":\"SetControlParameter\",\"params\":{\"/battery/rate\":\"-1500\"}},{\"topic\":\"SetControlParameter\",\"params\":{\"/grid/target\":\"-1250\"}}]"
		}
	}
	```

=== "ExecuteInstructions example decoded"

	The two instructions in the example `instructions` property look like this, when decoded into a JSON array:

	```json
	[
		{"topic": "SetControlParameter", "params": {"/battery/rate": "-1500"}},
		{"topic": "SetControlParameter", "params": {"/grid/target": "-1250"}}
	]
	```


## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `failFast` | When `true` then instruction processing should stop immediately after any nested instruction fails to complete successfully. When `false` (the default) then all instructions should be executed, regardless of any individual failures. If the `successMode` is `Some` this parameter is ignored. |
| `instructions` | A JSON array of nested instruction objects. Consult the documentation for each instruction topic you want to use for the syntax required. Note that the `nodeId` property is **not** needed on these instructions, as it is assumed to be the ID of the node executing the `ExecuteInstructions` instruction. |
| `successMode` | Either `Every` (the default) or `Some`. When `Every` then all nested instructions must complete successfully for the `ExecuteInstructions` to end in the `Completed` state, otherwise it will end in the `Declined` state. When `Some` then at least one instruction must complete successfully for the `ExecuteInstructions` to end in the `Completed` state. An instruction is considered _successful_ if it is handled and returns a status in either `Completed` or `Executing` state. |

## Result

A `Completed` state indicates the instructions where executed successfully, according to the
`successMode` and `failFast` parameter logic described in the previous section.
