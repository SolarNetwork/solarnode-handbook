# Local instructions

SolarNode provides the [`net.solarnetwork.node.reactor.InstructionExecutionService`][InstructionExecutionService]
API for plugins that want to issue _local_ instructions within SolarNode itself.

=== "InstructionExecutionService API"

	```java
	public interface InstructionExecutionService {

		/**
		 * Execute a single instruction.
		 *
		 * @param instruction
		 *        the instruction to execute
		 * @return the resulting status for the instruction, or {@code null} if not
		 *         handled
		 */
		@Nullable
		InstructionStatus executeInstruction(Instruction instruction);

	}
	```

=== "Example Blueprint XML"

	```xml
	<!-- reference as an OptionalService like this: -->
	<bean id="instructionExecutionService"
			class="net.solarnetwork.common.osgi.service.DynamicServiceTracker">
		<argument ref="bundleContext"/>
		<property name="serviceClassName"
			value="net.solarnetwork.node.reactor.InstructionExecutionService"/>
		<property name="sticky" value="true"/>
	</bean>


	<!-- or as a direct reference like this: -->
	<reference id="instructionExecutionService"
		interface="net.solarnetwork.node.reactor.InstructionExecutionService"/>
	```

The `net.solarnetwork.node.reactor.InstructionUtils` class provides some [`createLocalInstruction()`
helper methods][create-local] for creating `Instruction` instances, which you then pass to the
`executeInstruction()` method.

!!! note

	Note that local instructions submitted to the `executeInstruction()` method are processed
	immediately, not asyncronously like external instructions.

=== "Create a local instruction example"

	```java
	// use the SetControlParameter instruction to update a runtime control value
	double desiredControlValue = calculateControlValue();
	InstructionExecutionService instrService = OptionalService.service(instructionExecutionService);
	Instruction instr = InstructionUtils.createLocalInstruction(
			InstructionHandler.TOPIC_SET_CONTROL_PARAMETER,
			"/setpoint/1",
			String.valueOf(desiredControlValue));
	InstructionStatus instrResult;
	if ( instrService != null ) {
		instrResult = instrService.executeInstruction(instr);
	} else {
		instrResult = createStatus(instr, InstructionState.Declined, Map.of(
				InstructionHandler.PARAM_MESSAGE,
				"No InstructionExecutionService available."));
	}
	// do something with the result here
	```

[create-local]: https://github.com/SolarNetwork/solarnetwork-node/blob/5.18.0/net.solarnetwork.node/src/net/solarnetwork/node/reactor/InstructionUtils.java#L121-L170
[InstructionExecutionService]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node/src/net/solarnetwork/node/reactor/InstructionExecutionService.java
