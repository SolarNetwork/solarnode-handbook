---
title: Instructions
---
# Instruction endpoints

The `/api/v1/sec/instr` endpoints mirror the [SolarUser Instruction API][sn-instr-api] and allow
external applications to issue instructions directly to SolarNode.

## POST `/instr/add/{topic}`

Queue an instruction to execute. See the [SolarUser endpoint][sn-instr-api] for more details.

!!! note

	SolarNode will attempt to execute the instruction before responding, which differs from
	how the SolarNetwork API works. Be sure to check the response `state` to see if the
	instruction was already executed or will execute in the future.

| POST | `/api/v1/sec/instr/add/{topic}` |
|:-----|:--------------------------------|
| `topic` | The [instruction topic](../../instructions/topics/index.md). |

The request body should be a JSON object with the following properties:

| Property | Type | Description |
|:---------|:-----|:------------|
| `parameters` | Object[] | An array of parameter objects, which each have `name` and `value` String properties. |
| `params`     | Object   | An object whose keys represent parameter names, with associated values. |

!!! tip

	If parameters are required for the instruction topic,  one of `parameters` or `params` should
	be used, but not both.

=== "Example request"

	Send a `POST /api/v1/sec/instr/add/SetControlParameter` request with the following JSON content:

	```json
	{
		"params": {
			"/switch/1": "1"
		}
	}
	```

=== "Response"

	```json
	{
		"success": true,
		"data": {
			"instructionId": 1782532316363,
			"state": "Completed",
			"statusDate": "2026-06-27 03:51:56.364468629Z"
		}
	}
	```

## POST `/instr/updateState`

Update the state of existing instructions.

| POST | `/api/v1/sec/instr/instr/updateState` |
|:-----|:--------------------------------|
| `id` | The ID of a single instruction to update. |
| `ids` | A comma-delimited list of instruction IDs to update. |
| `state` | The desired state to set the matching instructions to; one of `Completed` or `Declined`. |

The request body should be an `application/x-www-form-urlencoded` encoding of the request parameters.

=== "Example request"

	Send a `POST /api/v1/sec/instr/updateState` request with the following content:

	```
	id=1782532316363&state=Declined
	```

=== "Response"

	```json
	{
		"success": true
	}
	```

## GET `/instr/view`

View one or more instruction satuses.

| POST | `/api/v1/sec/instr/instr/view` |
|:-----|:--------------------------------|
| `id` | The ID of a single instruction to view. |
| `ids` | A comma-delimited list of instruction IDs to view. |

The request body should be an `application/x-www-form-urlencoded` encoding of the request parameters.

!!! warning

	Instructions created with the local [`/add`](#post-instraddtopic) endpoint that end up
	executing and the result returned in the response will **not** be available from this
	endpoint, as those instructions are not persisted in SolarNode for future execution.

=== "Example request"

	Send a `POST /api/v1/sec/instr/updateState` request with the following content:

	```
	id=1782532316364
	```

=== "Response"

	```json
	{
		"success": true,
		"data": {
			"id": 1782532316364,
			"topic": "SetControlParameter",
			"instructionDate": "2026-06-27 04:06:57.939226Z",
			"parameters": [
				{
					"name": "test/switch",
					"value": "1"
				}
			],
			"state": "Received",
			"statusDate": "2026-06-27 16:07:00.017207Z"
		}
	}
	```

[sn-instr-api]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarUser-API#queue-instruction
