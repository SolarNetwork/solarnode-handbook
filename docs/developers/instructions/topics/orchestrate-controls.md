---
title: OrchestrateControls
---
# `OrchestrateControls` topic

This instruction can be used to schedule a set of control changes in response to an
`OrchestrateControls` instruction. For example, this could be used to execute a hot-water load shift
scenario like:

{==

* _in 1 hour turn the hot water thermostat up by 10℃_
* _in 2 hours turn the hot water cylinder off_
* _in 4 hours turn the hot water thermostat down by 10℃ and turn the hot water cylinder on_

==}

!!! info

	This instruction is supported by the [Control Conductor][plugin] SolarNode plugin, which is
	provided by the [solarnode-app-control-core][package] package in SolarNodeOS.

At a high level, the Control Conductor is configured with a set of _tasks_ that each specify a
control to update and when to update it, relative to an _orchestration date_.  The
`OrchestrateControls` instruction specifies which Control Conductor component to execute and the
orchestration date to execute at.

=== "OrchestrateControls example"

	```json
	{
		"nodeId": 123,
		"topic": "OrchestrateControls",
		"parameters": [
			{
				"name": "service",
				"value": "HVAC DR"
			},
			{
				"name": "date",
				"value": "2023-04-05T03:10:00Z"
			},
			{
				"name": "mode",
				"value": "Heat"
			},
			{
				"name": "duration",
				"value": "PT1M"
			}
		]
	}
	```

=== "OrchestrateControls example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "OrchestrateControls",
		"params": {
			"service"  : "HVAC DR",
			"date"     : "2023-04-05T03:10:00Z",
			"mode"     : "Heat",
			"duration" : "PT1M"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `date`    | The _orchestration date_ to execute the task schedule at, as a Unix millisecond epoch integer like `1680664200000` or an ISO 8601 instant like `2023-04-05T03:10:00Z`. |
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `service` | The **Service Name** of the Control Conductor to execute. |
| _extra_ | Other parameters can be provided, see [Extra parameters](#extra-parameters) for more details. |

### Extra parameters

Any parameter included in the  `OrchestrateControls` instruction will be available as a placeholder
for use in any configured task's **Control**, **Offset**, and **Value** settings. If **Value** is
configured as an expression, then the parameters will be available directly within the expression.


## Result

A `Completed` state indicates the orchestration has been scheduled successfully.

[plugin]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.control.conductor
[package]: https://github.com/SolarNetwork/solarnode-os-packages/tree/develop/solarnode-app-control-core/debian
