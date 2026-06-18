---
title: STOMP
---
# STOMP integration

SolarNode provides a [STOMP][stomp] TCP server that supports setup and configuration tasks in an
external application. A primary use case for this integration is to enable an application on a
Bluetooth-enabled phone to provide an easy-to-use UI for performing basic SolarNode setup tasks,
such as confirming that SolarNode is connected to the internet or changing the WiFi settings.

!!! note

	This integration is provided by the [net.solarnetwork.node.setup.stomp][stomp-plugin] plugin, which
	is available in the [solarnode-app-stomp-setup][solarnode-app-stomp-setup] package in SolarNodeOS.

## About STOMP

STOMP is a well-established and simple text-based bi-directional communication protocol that has
similarities to the structure of HTTP 1.x requests. Unlike HTTP's request/response style, however,
STOMP adheres to a publish/subscribe messaging model, using a long-lasting connection to allow for
real-time bi-directional communication.

!!! info

	STOMP is often used as the sub-protocol on WebSocket connections. In fact, SolarNode uses
	STOMP on its [WebSocket integration](../websocket/index.md).

A STOMP message is called a _frame_ and is structured as lines of text, each line ending with an
`EOL` (`\n` or ASCII `0x10`) like:

```
COMMAND
header1:value1
header2:value2

Body^@
```

The `COMMAND` is like a HTTP verb, and is one of the values defined in the STOMP standard. The
command is followed by zero or more header key/value pairs, much like HTTP headers. A blank line
follows that, followed by zero or more characters  representing the message body, finished with a
`NULL` byte, represented by `^@` for ++ctrl+"@"++.

!!! tip

	See the [STOMP][stomp] site fore more information about the protocol.

Here is a fictional request/response style message interaction using the SolarNode STOMP integration:

=== "Send message"

	Post a message to the `/setup/hello` topic:

	```
	destination:/setup/hello
	request-id:2

	^@
	```

=== "Receive message"

	The STOMP server sends a "Hi there!" JSON string message in response to the `request-id:2`
	message back to the client like this:

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

!!! warning

	Note that STOMP is a bi-directional, publish/subscribe style protocol, unlike HTTP's
	request/response style. Even so, it is often convenient to think about a STOMP client sending a
	"request" to the server, and the server replying with a "response", with the `request-id` header
	used to correlate the two messages into a request/response style exchange.

[stomp]: https://stomp.github.io/
[stomp-plugin]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.setup.stomp
[solarnode-app-stomp-setup]: https://github.com/SolarNetwork/solarnode-os-packages/tree/develop/solarnode-app-stomp-setup/debian
