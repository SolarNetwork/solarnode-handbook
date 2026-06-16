# Instructions

SolarNetwork supports the concept of sending an _instruction_ to a node. An instruction is a request
for the node to take some sort of action, and send the status of performing that action back to
SolarNetwork. An action could be something like _"turn switch `/relay/1` off"_ or _"give me a list
of the installed plugins"_.

## Instruction data model

The core entity in the SolarNode instruction architecture is the [`Instruction`][Instruction], which
is modeled as a _topic_ (the desired action) with _named parameters_ (dynamic attributes).

=== "Example JSON"

	```json
	{
		"instructorId":  "in.solarnetwork.net",
		"id": 12345,
		"nodeId": 123,
		"topic": "SystemConfiguration",
		"instructionDate": "2024-06-11 21:39:09.154867Z",
		"statusDate": "2024-06-11 21:39:09.649036Z",
		"parameters": [
			{"name": "service", "value": "net.solarnetwork.node.packages"}
		],
		"state": "Completed",
		"resultParameters": {
			"result": [
				{
					"name": "solarnode-base",
					"version": "4.1.1-1",
					"installed": true
				}
			]
		}
	}
	```

	!!! note

		The `nodeId` property is used in the SolarNetwork API, but not within SolarNode itself
		as the ID of the node executing the instruction is assumed.

=== "Example JSON (compact)"

	When working with instructions it is often convenient to treat the `parameters` list like a
	map, or when working with JSON, an object. The Example JSON could be expressed more compactly
	using a `params` object property in place of the `parameters` array:

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfiguration",
		"params": {
			"service": "net.solarnetwork.node.packages"
		}
	}
	```

=== "Instruction API"

	```java
	package net.solarnetwork.node.reactor;

	public interface Instruction {

		String getInstructorId();

		@Nullable
		Long getId();

		@Nullable
		String getTopic();

		@Nullable
		Instant getInstructionDate();

		Iterable<String> getParameterNames();

		boolean isParameterAvailable(String parameterName);

		@Nullable
		String getParameterValue(String parameterName);

		String @Nullable [] getAllParameterValues(String parameterName);

		@Nullable
		InstructionStatus getStatus();

	}
	```

	!!! note

		When an `Instruction` is serialized to JSON, the `InstructionStatus` object's properties
		are added directly to the root JSON object, not as a nested `status` object. That is
		why in the _Example JSON_ section you see `state` and `resultParameters` properties on
		the root JSON object: they come from the `InstructionStatus` instance of the `Instruction`.


=== "InstructionStatus API"

	```java
	package net.solarnetwork.node.reactor;

	public interface InstructionStatus {

		@Nullable
		InstructionState getAcknowledgedInstructionState();

		@Nullable
		Long getInstructionId();

		@Nullable
		InstructionState getInstructionState();

		@Nullable
		Instant getStatusDate();

		@Nullable
		Map<String, ?> getResultParameters();

	}
	```

=== "InstructionState enum"

	```java
	package net.solarnetwork.domain;

	public enum InstructionStatus.InstructionState {

		/**
		 * The instruction state is not known.
		 */
		Unknown,

		/**
		 * The instruction has been queued on the sender for the recipient.
		 */
		Queued,

		/**
		 * The instruction is in the process of being queued, potentially
		 * jumping to the received state if an immediate acknowledgement is
		 * possible.
		 */
		Queuing,

		/**
		 * The recipient has acknowledged receipt of the instruction, but has
		 * not been processed yet. It will be processed at as soon as possible.
		 */
		Received,

		/**
		 * The instruction has been received and is being executed by the
		 * recipient currently.
		 */
		Executing,

		/**
		 * The instruction was received but the recipient has declined to
		 * execute the instruction.
		 */
		Declined,

		/**
		 * The instruction was received and has been executed.
		 */
		Completed;

	}
	```

## Asynchronous execution model

The SolarNode instruction exectuion model is asynchronous. Instructions can originate from calls to
the SolarNetwork [`/instr/add/{topic}`][add-instr] endpoint, or SolarNode plugins can issue _local_
instructions as a sort of inter-plugin remote procedure call. After an instruction has been
submitted, the state of the instruction will change over time as the node specified in the
instruction receives and processes the instruction. In general an instruction's state will
transition like this:

 1. `Queuing`
 2. `Queued`
 3. `Received`
 4. `Executing`
 5. `Completed`

As the instruction's state changes, SolarNode publishes the updated state information back to
SolarNetwork so external applications can track the overall status of an instruction's lifecycle.


## Deferred instructions

Any instruction's execution can be deferred to a specific (future) date by including an
`executionDate` instruction parameter. The value of this parameter must be either:

 1. a Unix millisecond epoch integer, for example `1680030000000`
 2. an ISO 8601 instant, for example `2023-03-28T19:00:00Z`

Here's an example of setting a control parameter with a specific execution date:

```json
{
	"nodeId": 123,
	"topic":"SetControlParameter",
	"params": {
  		"/switch/1": "1",
  		"executionDate": "2023-03-28T19:00:00Z"
	}
}
```

SolarNode will accept deferred instructions, save them internally in the `Received` state, and then
execute them at the given date.

### Deferred instruction notes

There are a few important things to note about deferred instructions:

 * SolarNode must be online and receiving instructions from SolarNetwork.
 * SolarNode's job execution time precision is configurable, and by default has **10 second**
   resolution. That means a deferred job could execute up to 10 seconds later than the specified date.
 * The execution date relies on the clock of the system SolarNode is running on; an inaccurate
   system clock will result in unexpected deferred instruction execution times.

### Cancelling a deferred instruction

If an instruction is in the `Queued` state, that means SolarNode has not yet received the
instruction. It can be cancelled by changing its state to `Declined` with the
[`/instr/updateState`][update-instr-state] endpoint.

If an instruction is in the `Received` state, that means SolarNode has received and saved the
instruction. A new [CancelInstruction](./topics/cancel-instruction.md) instruction
can be issued to the same node to cancel such an instruction. For example:

```json
{
	"topic":"CancelInstruction",
	"params": {
		"id": "12345"
	}
}
```



[add-instr]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarUser-API#queue-instruction
[Instruction]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node/src/net/solarnetwork/node/reactor/Instruction.java
[update-instr-state]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarUser-API#update-instruction-state
