---
title: REST
---
# REST integration

SolarNode provides a JSON REST HTTP API that supports various functions for external applications.

## Authentication

The REST API requires clients to authenticate using the [SNWS2][snws2] scheme (the same scheme
used by the SolarNetwork API). Security tokens are created and managed on the
[Security Tokens](../../../users/setup-app/security-tokens.md) page in SolarNode.

## Response structure

General JSON responses are returned using a basic result object structure shared with the
SolarNetwork API, including the following properties:

| Property | Type | Description |
|:---------|:-----|:------------|
| `success` | Boolean  | Either `true` or `false` representing the success of the request. |
| `code`    | String   | An error code, used sometimes when `sucess` is `false`. |
| `message` | String   | An message, typically used for error messages when `success` is `false`. |
| `errors`  | Object[] | A list of error detail objects. |
| `data`    | _any_    | Result data, the form of which depends on the endpoint. |

```json title="Example response"
{
	"success": true,
	"data": {
		"token": "abc123def456"
	}
}
```

[snws2]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-authentication-scheme-V2
