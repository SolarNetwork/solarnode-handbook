---
title: InstructionHandler
---
# InstructionHandler API

The `net.solarnetwork.node.reactor.InstructionHandler` API is implemented by services that
want to respond to and handle instructions in SolarNode.

=== "InstructionHandler API"

	```java
	package net.solarnetwork.node.reactor;

	public interface InstructionHandler {

		/**
		 * Test if a topic is handled by this handler.
		 *
		 * @param topic
		 *        the topic
		 * @return {@literal true} only if this handler can execute the job for the
		 *         given topic
		 */
		boolean handlesTopic(@Nullable String topic);

		/**
		 * Process an instruction.
		 *
		 * @param instruction
		 *        the instruction to process
		 * @return the status for the instruction, or {@code null} if the
		 *         instruction was not handled
		 */
		@Nullable
		InstructionStatus processInstruction(Instruction instruction);

	}
	```

=== "Example implementation"

	```java
	package net.solarnetwork.node.setup.example;

	import java.time.Instant;
	import java.util.Map;
	import net.solarnetwork.node.reactor.Instruction;
	import net.solarnetwork.node.reactor.InstructionHandler;
	import net.solarnetwork.node.reactor.InstructionStatus;
	import net.solarnetwork.node.reactor.InstructionUtils;

	public class ExampleHandler implements InstructionHandler {

		@Override
		public boolean handlesTopic(@Nullable String topic) {
			return InstructionHandler.TOPIC_SYSTEM_CONFIGURE.equals(topic);
		}

		@Override
		public @Nullable InstructionStatus processInstruction(Instruction instruction) {
			// verify we handle this topic
			if ( instruction == null || !handlesTopic(instruction.getTopic()) ) {
				return null;
			}

			// validate the service parameter == "example"
			final String service = instruction.getParameterValue(InstructionHandler.PARAM_SERVICE);
			if ( !"example".equals(service) ) {
				return null;
			}

			// a real handler would now do something useful, and then return the status
			return InstructionUtils.createStatus(instruction, InstructionState.Completed, Instant.now(),
					Map.of(InstructionHandler.PARAM_SERVICE_RESULT, "ok"));
		}
	}
	```

=== "Example Blueprint XML"

	```xml
	<service interface="net.solarnetwork.node.reactor.InstructionHandler">
		<service-properties>
			<entry key="instruction">
				<list>
					<value>#{T(net.solarnetwork.node.reactor.InstructionHandler).TOPIC_SYSTEM_CONFIGURE}</value>
				</list>
			</entry>
		</service-properties>
		<bean class="net.solarnetwork.node.setup.example.ExampleHandler"/>
	</service>
	```

The API has just two methods to be implemented. The `handlesTopic(String)` method needs to return
`true` for any instruction topic the handler supports. The `processInstruction(Instruction)` method
does the work of executing a given `Instruction`.

Even though a handler might return `true` from `handlesTopic()` for a given topic, it might not
actually support executing a specific `Instruction` with that topic. A good example of this is the
`SetControlParameter` instruction topic, which expects a parameter named after the Control ID the
instruction is meant to operate on. There could be _many_ handler component instances active in
SolarNode when such an instruction arrives, but only one which actually has responsibility for the
given Control ID. SolarNode will call the `processInstruction()` method on each handler instance in
an undefined order, and stop after the first one that returns a non-null `InstructionStatus` object.

!!! tip

	A handler should return `null` from `processInstruction()` if it cannot claim responsibility
	for the instruction's intended target.

