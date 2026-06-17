---
title: StopRemoteSsh
---
# `StopRemoteSsh` topic

This instruction requests the node to disconnect a SSH connection, presumably previously started via
the [`StartRemoteSsh`](./start-remote-ssh.md) instruction. It requires the same parameters as
`StartRemoteSsh`, so the connection can be uniquely identified by the parameters.

=== "StopRemoteSsh example"

	```json
	{
		"nodeId": 123,
		"topic": "StopRemoteSsh",
		"parameters": [
			{"name": "user", "solar"},
			{"name": "host", "value": "ssh.solarnetwork.net"},
			{"name": "port", "8022"},
			{"name": "rport", "43330"}
		]
	}
	```

=== "StopRemoteSsh example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "StopRemoteSsh",
		"params": {
			"user": "solar",
			"host": "ssh.solarnetwork.net",
			"port": "8022",
			"rport": "43330"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `host` | The host to SSH to (hostname or IP address). |
| `port` | The IP port on `host` to connect on. |
| `rport` | The first of two reverse port forwarding ports the server will allow. The port provided here must be forwarded back to a SSH server on the node, typically port `22`. For example, if `rport` is `43330` then an OpenSSH a command line argument like `-R 127.0.0.1:43330:localhost:22` would be used. A second reverse port is implied, as `rport + 1`, that must be forwarded back to the HTTP server on the node, typically port `8080`. Continuing the example, an OpenSSH a command line argument like `-R 127.0.0.1:43331:localhost:8080` would be used. |
| `user` | The username to connect as. |


## Result

A `Completed` state indicates the node has closed the SSH connection.
