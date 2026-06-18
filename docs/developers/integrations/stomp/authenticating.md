# Authenticating

Once a client receives a [`CONNECTED` frame](./connecting.md#connected-frame), it must authenticate
the session, using the scheme specified in the `authenticate` header. Authentication is performed by
sending a `SEND` frame with a `destination` header value of `/setup/authentication` and an
`authorization` header with the appropriate credentials, the syntax of which depends on the scheme
used. The only supported scheme at this time is [`SNS`](#sns-authentication-scheme).

!!! note

	The scheme might require additional headers, some of which may be provided on the `CONNECTED`
	frame. Consult the scheme documentation for more details.

After publishing the `SEND` authentication frame, if the authentication is successful nothing will
happen and the client application can move on to [subscribing to the setup topic](./subscribing.md)
to start interacting with the STOMP Server. If the authentication fails, the server will send an
`ERROR` frame to the client and close the connection.

## SNS authentication scheme

The [`SNS`][sns] scheme uses an HMAC+SHA256 digest of various parts of the STOMP frame, signed using
a secret key derived from the SolarNode user's hashed password. SolarNode does not store a
plain-text version of a user's password, so that is why the hashed password is used for the signing
key.

The required `SEND` headers for SNS authentication are:

| Header | Description |
|:-------|:------------|
| `authorization` | The SNS authorization value, e.g. `SNS Credential=me@example.com,SignedHeaders=date,Signature=168365...`. |
| `date` | The request date, e.g. `Mon, 16 Aug 2021 02:27:39 GMT`. |

``` title="Example SEND frame for authentication"
SEND
destination:/setup/authentication
date:Mon, 16 Aug 2021 02\c27\c39 GMT
authorization: SNS Credential=me@example.com,SignedHeaders=date,Signature=168365...
```

!!! warning "Escaping the date header for STOMP"

	When encoding the `date` header, be sure to escape the `:` character in the date value with `\c`,
	per the [STOMP specification](https://stomp.github.io/stomp-specification-1.2.html#Value_Encoding).
	This encoding is _only_ needed in the STOMP header, _not_ in the SNS signature calculation.

The [canonical request message][sns-crm] of the signing message must use the following values:

| Component | Description |
|:----------|:------------|
| Verb | Must be `SEND`. |
| Path | Must be `/setup/authenticate`. |
| Signed header names | Only `date` is required. |

## SNS authentication example

Here is a basic example in Java that uses the
[SnsAuthorizationBuilder][SnsAuthorizationBuilder.java] class to generate the required
`authorization` and `date` headers required by the SNS scheme:

```java
String secret = "value-derived-from-password"; // depends on `auth-hash` CONNECTED header
SnsAuthorizationBuilder authBuilder = new SnsAuthorizationBuilder("me@example.com")
	.date(now)
	.verb("SEND")
	.path("/setup/authenticate");
String authHeader = authBuilder.build(secret);
String dateHeader = authBuilder.headerValue("date");
```

## BCrypt secret derivation

The `auth-hash:bcrypt` [`CONNECTED`](./connecting.md#connected-frame) header indicates that the
BCrypt digest algorithm must be used to derive the SNS secret value used to sign the request, that
in turn converted into by a hex-encoded SHA-256 digest. At a high level the algorithm in pseudo-code
looks like this:

```
secret := Hex(Sha256(BCrypt(password, salt)))
```

The `auth-hash-param-salt` header value from the [`CONNECTED`](./connecting.md#connected-frame)
frame will determine the BCrypt salt that must be used to digest the user's plain-text password. The
salt will take the form of

```
$2a$10$1234567890123456789012
```

Here `$2a` indicates the version of BCrypt used, `$10` indicates the number of iterations used, and
everything after the final `$` is the Base64 encoded salt used.

### BCrypt secret example

The [SnsAuthorizationBuilder][SnsAuthorizationBuilder.java] class can be used to generate the
required `authorization` header value. See [this example][sns-auth-builder-example-java] for
more details; here is that example distilled:

```java
String salt = "$2a$10$upVbEZHge9Iph1NN3L6ENO"; // from auth-hash-param-salt CONNECTED header
String secret = DigestUtils.sha256Hex(BCrypt.hashpw("password123", salt));
SnsAuthorizationBuilder authBuilder = new SnsAuthorizationBuilder("me@example.com")
		.date(now)
		.verb("SEND")
		.path("/setup/authenticate");
String authHeader = authBuilder.build(secret);
String dateHeader = authBuilder.headerValue("date");
```

[SnsAuthorizationBuilder.java]: https://github.com/SolarNetwork/solarnetwork-common/blob/develop/net.solarnetwork.common/src/net/solarnetwork/security/SnsAuthorizationBuilder.java
[sns-auth-builder-example-java]: https://github.com/SolarNetwork/solarnetwork-node/blob/0d387a6ceb973c88c87e45ac7d0cd9a0bc95ba02/net.solarnetwork.node.setup.stomp.test/src/net/solarnetwork/node/setup/stomp/test/StompSetupServerHandlerTests.java#L260-L308
[sns]: https://github.com/SolarNetwork/solarnetwork/wiki/SNS-authentication-scheme
[sns-crm]: https://github.com/SolarNetwork/solarnetwork/wiki/SNS-authentication-scheme#canonical-request-message
