---
title: SetControlParameter
---
# `SetControlParameter` topic

This instruction sets the value of a SolarNode [control](../../../users/controls/index.md) to a
specific value. The instruction should include a single parameter named after the **control ID**
whose value should be updated, whose corresponding value is the desired state of the control. For
example, to set the `/switch/1` control to `1` an instruction would look like this:

=== "SetControlParameter example"

	```json
	{
		"nodeId": 123,
		"topic": "SetControlParameter",
		"parameters": [
			{"name": "/switch/1", "value": "1"}
		]
	}
	```

=== "SetControlParameter example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "SetControlParameter",
		"params": {
			"/switch/1": "1"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `{controlId}` | The **control ID** of the control whose value should be updated, with the associated parameter value holding the desired value to set the control to. |
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |

## Result

A `Completed` state indicates the control was updated successfully. A `Declined` state
indicates the control was not updated successfully, or does not exist.
