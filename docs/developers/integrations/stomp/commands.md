---
title: Commands
---
# Sending commands

The STOMP server will handle commands via `SEND` frames posted by the client and send the
result as a `MESSAGE` frame using the same `destination` header value as used in the original
`SEND` frame. Commands are processed in an asynchronous fashion, so multiple commands can be
active at once, and the order of their replies are undefined. Clients can keep track of `SEND` and
`MESSAGE` pairs by including a unique `request-id` header value in each `SEND` frame. The server
will include that same header in the associated `MESSAGE` response frame.

Here is an example `SEND` frame to execute the `/setup/datum/latest` command, which returns
a JSON array of the most recently captured datum in SolarNode:

=== "Example SEND to get the latest datum"

	```
	SEND
	destination:/setup/datum/latest
	request-id:1

	^@
	```

=== "MESSAGE response"

	```
	MESSAGE
	destination:/setup/datum/latest
	status:200
	message-id:26188729
	subscription:0
	request-id:1
	content-type:application/json;charset=utf-8
	content-length:159

	[{"created":"2021-08-19 02:30:10.005Z","sourceId":"Mock Energy Meter","i":{"voltage":234.99959,"frequency":50.499973,"watts":11214},"a":{"wattHours":6118188}}]^@
	```

!!! info

	See the STOMP specification for more details on the
	[`SEND`](https://stomp.github.io/stomp-specification-1.2.html#SEND) and
	[`MESSAGE`](https://stomp.github.io/stomp-specification-1.2.html#MESSAGE) frames.

## Command handling in SolarNode

Internally, each STOMP `SEND` setup command will be converted to a [local
instruction](../../instructions/local-instructions.md) using the [`SystemConfigure`
topic](../../instructions/topics/system-configure.md). The instruction result status will then be converted into
a `MESSAGE` response frame and and published back to the client.

### Creating the request `SystemConfigure` instruction

The `SystemConfigure` instruction will be created from the `SEND` frame using the following logic:

 1. The `destination` header value will be provided as a `service` instruction parameter.
 2. All other headers are added as instruction parameters, named after the header name with its
    associated value.
 3. Any frame content (body) will be assumed to be a UTF-8 string and will be provided as an `arg`
    instruction parameter.

!!! note

	The `arg` instruction parameter value be the raw string value from the `SEND` frame. It will not
	be parsed in any way. The `SEND` frame `content-type` header will be provided as an instruction
	parameter so a [handler](../../instructions/handler.md) can understand how to interpret the
	value.

### Creating the response `MESSAGE`

After executing the `SystemConfigure` instruction, the resulting `InstructionStatus` will be
converted into a STOMP `MESSAGE` frame using the following logic:

 1. A `status` header will be derived from the `InstructionState` or `statusCode` result parameter.
 2. A `message` header will be derived from any thrown exception, or a `message` result parameter.
 3. Any `result` result parameter will be converted to JSON and used as the frame content.

 The `MESSAGE` frame `status` header will be set according to the `InstructionState` returned by the
 handler:

 | InstructionState | Status value | Description |
 |:-----------------|:-------------|:------------|
 | `Completed`      | `200`        | The command was executed successfully. |
 | `Executing`      | `202`        | The command is executing asynchronously. |
 | _null_           | `404`        | No handler accepted processing the command. |
 | `Declined`       | `422`        | The command was recognized but not executed because of a client problem. |
 | _exception_      | `500`        | The handler threw an exception. The `message` header will contain the exception  message. |

 The instruction handler can override this default mapping by returning a `statusCode` result
 parameter with an integer value. Additionally the handler can provide a `message` result parameter
 to use for the `message` header.

=== "Example SolarNode command handler"

	Here is an example `InstructionHandler` snippet, that responds to a `/setup/hello` command
	and returns a string result `Hi there!`:

	```java
	public boolean handlesTopic(String topic) {
		return InstructionHandler.TOPIC_SYSTEM_CONFIGURE.equals(topic);
	}

	public InstructionStatus processInstruction(Instruction instruction) {
		if ( instruction == null || !handlesTopic(instruction.getTopic()) ) {
			return null;
		}
		final String topic = instruction.getParameterValue(InstructionHandler.PARAM_SERVICE);
		if ( !"/setup/hello".equals(topic) ) {
			return null;
		}
		final String result = "Hi there!";
		return InstructionStatus.createStatus(instruction, InstructionState.Completed, Instant.now(),
			Map.of(InstructionHandler.PARAM_SERVICE_RESULT, result));
	}
	```

=== "Invoking handler with `SEND` frame"

	To have this handler invoked, a client would post a `SEND` frame like this:

	```
	destination:/setup/hello
	request-id:2

	^@
	```

=== "Command response with `MESSAGE` frame"

	The Setup STOMP server would post a `MESSAGE` back to the client like this:

	```
	destination:/setup/hello
	status:200
	message-id:1234567
	subscription:0
	request-id:2
	content-type:application/json;charset=utf-8
	content-length:11

	"Hi there!"^@

	```

	!!! tip

		Note how the response is a JSON string, enclosed in double-quotes. All messages returned
		from the server will be encoded into JSON.
