---
title: Instructor
---
# Instructor Datum Filter

This component provides a way to issue dynamic node-local [instructions](../instructions/index.md)
based on expressions. A _predicate_ expression is evaluated to conditionally issue a set of
associated instructions with a set of dynamic parameters.

--8<-- "snippets/users/datum-filters/provided-by-standard-filter.md"

## Settings

<figure markdown>
  ![Instructor filter component settings](../../images/users/datum-filters/instructor-settings@2x.png){width=1024 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description |
|:-------------------|:------------|
| [Instructor Configurations](#instructor-settings) | A list of instructor configurations. Use the ++plus++ and ++minus++ buttons to add/remove as many configurations as you need. |

### Instructor settings

<figure markdown>
  ![Instructor Configuration settings](../../images/users/datum-filters/instructor-predicate-settings@2x.png){width=1024 loading=lazy}
</figure>

Each Instructor Configuration supports the following settings:

| Setting             | Description |
|:--------------------|:------------|
| Predicate           | The expression that must evaluate to `true` to generate the configured instructions. |
| Expression Language | The [expression language][expr] to write **Predicate** in. |
| [Instructions](#instruction-settings) | A list of instruction configurations. Use the ++plus++ and ++minus++ buttons to add/remove as many configurations as you need. |

### Instruction settings

<figure markdown>
  ![Instruction Configuration settings](../../images/users/datum-filters/instructor-instruction-settings@2x.png){width=1024 loading=lazy}
</figure>

Each Instruction Configuration supports the following settings:

| Setting                | Description |
|:-----------------------|:------------|
| Instruction Topic      | The [instruction topic](../instructions/topics/index.md) to generate. |
| [Instruction Parameters](#instruction-parameter-settings) | An optional list of instruction parameter configurations. Use the ++plus++ and ++minus++ buttons to add/remove as many configurations as you need. |
| [Result Expressions](#result-expression-settings)     | An optional list of instruction result expression configurations. Use the ++plus++ and ++minus++ buttons to add/remove as many configurations as you need. |

### Instruction Parameter settings

<figure markdown>
  ![Instruction Parameter Configuration settings](../../images/users/datum-filters/instructor-instruction-parameter-settings@2x.png){width=1024 loading=lazy}
</figure>

Each Instruction Parameter Configuration supports the following settings:

| Setting                | Description |
|:-----------------------|:------------|
| Parameter Name  | The instruction parameter name to use. |
| Parameter Value | The instruction parameter value. Can be an expression if **Parameter Expression Language** is also configured. |
| Parameter Expression Language | The expression language **Parameter Value** is written in, or omit to treat **Parameter Value** as plain text. |

### Result Expression settings

<figure markdown>
  ![Result Expression Configuration settings](../../images/users/datum-filters/instructor-result-expression-settings@2x.png){width=1024 loading=lazy}
</figure>

Each Result Expression Configuration supports the following settings:

| Setting                | Description |
|:-----------------------|:------------|
| Result Datum Property | A datum property name to save the expression result to. Leave empty to evaluate the expression without saving any datum property. |
| Result Datum Property Type | The datum property type to save the expression result as, if **Result Datum Property** also configured. |
| Result Expression | The expression to evaluate. |
| Result Expression Language | The expression language **Result Expression** is written in. |

## Expressions

The **Predicate** and **Parameter Value** expresions are normal [datum expressions](../expressions.md).
The **Result Expression** also includes the following additional variables:

| Variable | Type | Description |
|:---------|:-----|:------------|
| `instruction` | The [Instruction](../instructions/index.md#__tabbed_1_3) object that was issued. |
| `instructionResult` | If the instruction was successfully issued, the [Instruction Status](../instructions/index.md#__tabbed_1_4) object with the result status and any result parameters generated. |

For example, you could generate a datum property based on the success of the executed instruction
with a **Result Expression** like:

```java title="Generate a true/false datum property based on the instruction result status"
has('instructionResult') && instructionResult.completed
```

## Example: Vehicle Gate Control

Imagine a vehicle service garage, where vehicles enter and leave a maintenance bay in one direction.
When a vehicle enters and stops in the bay, a safety gate should close behind the vehicle, so
another vehicle cannot enter the same bay. When the vehicle later drives away and leaves the bay,
the safety gate should open behind the vehicle.

Also, when a vehicle enters the bay, the safety gate should only close if the vehicle stays in the
bay for at least **2 minutes**. Sometimes a vehicle enters a bay but does not stop, and as the gate
is a bit slow to close and re-open, the maintenance crew does not want to be forced to wait unless a
vehicle really is staying in the bay for mainenance. After a vehicle leaves the bay, the gate should
go up **20 seconds** later.

Now imagine a SolarNode is deployed on site, and connected to a sensor detects when a vehicle is
present in the bay and activates a "present" signal that SolarNode reads as a `1` value. When no
vehicle is present in the bay the sensor signal reads as a `0` value. Essentially SolarNode has a
datum stream that looks like this:

```json title="Vehicle presence sensor datum example"
{
	"sourceId": "/vehicle/presence",
	"timestamp": "2026-07-14T12:00:00Z",
	"isPresent": 1 // (1)!
}
```

1. Will be `1` when a vehicle is present, or `0` otherwise.

An Instructor Datum Filter can be configured on this datum stream, and use **Predicate** expressions
to detect changes in the `isPresent` property and issue instructions to make the gate go up or down,
as appropriate. For this example we will assume two [Control Conductor](../controls/conductor.md)
components are configured, named `Motor Down` and `Motor Up`, that have the logic necessary to make
the gate go down and up, respectively. The logic will be configured with 2 Instructor Configurations:
one for the "go down" logic and another for the "go up" logic. [LocalState](../local-state.md)
records are used to maintain state between executions of the Instructor.

=== "Go down logic"

	1. Predicate: test for a change on the `isPresent` property from `0` to `1` with the help of
	   a `present` Local State record.
	2. If the predicate is true, issue `CancelInstruction` instructions for any previous "go up"
	   and "go down" instructions. See item 4 below.
	3. Initiate the "go down" sequence by issuing a `OrchestrateControls` instruction for the
	   `Motor Down` Control Conductor component. The `date` instruction parameter is calcualted
	   to be **2 minutes in the future**.
	4. Capture the `OrchestrateControls` instruction ID and save to a `down-instruction-id`
	   Local State record. See item 2 above.

	![Go Down settings](../../images/users/datum-filters/instructor-example-settings-motor-down@2x.png){width=1024 loading=lazy}

=== "Go up logic"

	1. Predicate: test for a change on the `isPresent` property from `1` to `0` with the help of
	   a `present` Local State record.
	2. If the predicate is true, issue `CancelInstruction` instructions for any previous "go up"
	   and "go down" instructions. See item 4 below.
	3. Initiate the "go up" sequence by issuing a `OrchestrateControls` instruction for the
	   `Motor Down` Control Conductor component. The `date` instruction parameter is calcualted
	   to be **20 seconds in the future**.
	4. Capture the `OrchestrateControls` instruction ID and save to a `up-instruction-id`
	   Local State record. See item 2 above.

	![Go Up settings](../../images/users/datum-filters/instructor-example-settings-motor-up@2x.png){width=1024 loading=lazy}

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[expr]: ../expressions.md
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
