# Connecting

To connect to the SolarNode STOMP server, open a TCP/IP socket connection to the host name or
IP address of SolarNode, using the port configured in the server's settings (the default is `8780`).

Once the TCP connection is established, you must then send a `CONNECT` or `STOMP` frame.

## `CONNECT` frame

The `CONNECT` frame is sent by the client and used to initiate a new _session_. It must be the
first frame sent by the connected client. The following frame headers are required:

| Header | Description |
|:-------|:------------|
| `accept-version` | Only `1.2` is allowed. |
| `host` | The host name or IP address of SolarNode. |
| `login` | The SolarNode login to use for the session. This login will be authenticated later. |

Upon successful receipt of a `CONNECT` frame the server will send a [`CONNECTED`](#connected-frame)
frame to the client.

``` title="Example CONNECT frame"
CONNECT
accept-version:1.2
host:solarnode
login:solar

^@
```

!!! info

	See the [STOMP specification](https://stomp.github.io/stomp-specification-1.2.html#CONNECT_or_STOMP_Frame)
	for more details on the `CONNECT` frame.

### `CONNECT` error messages

If a client sends a `CONNECT` frame on a connection which has already established a session,
an `ERROR` frame will be sent to the client with a `message` header value of _Already connected._.

``` title="Example ERROR: already connected"
ERROR
message:Already connected.

^@
```

## `CONNECTED` frame

The `CONNECTED` frame is sent by the server and used to indicate a new _setup session_ has been
successfully started. After receipt of this frame a client must [authenticate](./authenticating.md).
The following frame headers will be returned:

| Header | Description |
|:-------|:------------|
| `version` | The STOMP version accepted by the server. Will be `1.2`. |
| `server` | The setup server name and version. |
| `session` | The unique ID of the setup session. |
| `message` | A request to authenticate. |
| `authenticate` | The required authentication scheme. Will be `SNS`. |
| `auth-hash` | The password digest algorithm to use. Will be `bcrypt`. |
| `auth-hash-param-*` | Any number of password digest algorithm parameters to use. |

The only value currently supported for `auth-hash` is `bcrypt`, and the only parameter returned
will be `auth-hash-param-salt`. These details must be used when [authenticating](./authenticating.md).

``` title="Example CONNECTED frame"
CONNECTED
version:1.2
server:SolarNode-Setup/1.0
session:a48c33d5-307e-4387-86a6-cd3cae372f78
message:Please authenticate.
authenticate:SNS
auth-hash:bcrypt
auth-hash-param-salt:$2a$10$upVbEZHge9Iph1NN3L6ENO

^@
```

!!! info

	See the [STOMP specification](https://stomp.github.io/stomp-specification-1.2.html#CONNECTED_Frame)
	for more details on the `CONNECTED` frame.

