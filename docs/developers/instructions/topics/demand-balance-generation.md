---
title: DemandBalanceGeneration
---
# `DemandBalanceGeneration` topic

This instruction asks a node to restrict active power generation to a maximum percentage value. It
is a specialized form of the [`SetControlParameter`](./set-control-parameter.md) instruction, and
works much the same way.

You must provide a single parameter where the *name* is the ID of the
[control](../../../users/controls/index.md) to manipulate and the *value* is an integer percentage
to set the control to. For example, to set the `/power/balance/1` control to `95` you would include
these parameters:

=== "DemandBalanceGeneration example"

	```json
	{
		"nodeId": 123,
		"topic": "DemandBalanceGeneration",
		"parameters": [
			{
				"name": "/power/balance/1",
				"value": "95"
			}
		]
	}
	```

=== "DemandBalanceGeneration example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "DemandBalanceGeneration",
		"params": {
			"/power/balance/1": "95"
		}
	}
	```


## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `{controlId}` | The **control ID** of the control whose balance should be updated, with the associated parameter value holding the desired integer percentage (0 - 100) to set the balance to. |
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |


## Result

A `Completed` state indicates the control was updated successfully. A `Declined` state
indicates the control was not updated successfully, or does not exist.


[spel]: https://github.com/SolarNetwork/solarnetwork/wiki/Spring-Expression-Language-Syntax
