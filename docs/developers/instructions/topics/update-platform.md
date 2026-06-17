---
title: UpdatePlatform
---
# `UpdatePlatform` topic

This instruction is supported by the [S3 Setup][s3-setup] SolarNode plugin, and can be used to
request a node to download and install either the latest or a specific version of a S3 Setup
package.

=== "UpdatePlatform example"

	```json
	{
		"nodeId": 123,
		"topic": "UpdatePlatform",
		"parameters": [
			{"name": "Version", "value": "101"}
		]
	}
	```

=== "UpdatePlatform example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "UpdatePlatform",
		"params": {
			"Version": "101"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `Version` | The package version to install. If not specified, the _latest available_ package should be installed. |

## Result

A `Completed` state indicates the node will update the platform.

[s3-setup]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.setup.s3
