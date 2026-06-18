---
title: Subscribing
---
# Subscribing to topics

Once successfully [authenticated](./authenticating.md), a client can subscribe to the `/setup/**` wild card topic to
receive messages from the server.

``` title="Example SUBSCRIBE frame"
SUBSCRIBE
id:0
destination:/setup/**

^@
```

!!! info

	See the [STOMP specification](https://stomp.github.io/stomp-specification-1.2.html#SUBSCRIBE) for more details
	on the `SUBSCRIBE` frame.
