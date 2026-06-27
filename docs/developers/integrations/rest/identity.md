---
title: Identity
---
# Identity endpoints

## GET `/whoami`

Use this endpoint to verify authentication is working.

| `GET` | `/api/v1/sec/whoami` |
|:-|:-|

No query parameters are supported. The response will be an object with the following properties:

| Property | Description |
|:---------|:------------|
| `token`  | The token ID used to authenticate the request. |

```json title="Example response"
{
	"success": true,
	"data": {
		"token": "abc123def456"
	}
}
```
