---
title: DatumSourceMetadataClearCache
---
# `DatumSourceMetadataClearCache` topic

This instruction asks a node to remove any cached [datum metadata][meta] for a set if source IDs, so
the node is forced to reload that metadata from SolarNetwork. Nodes cache datum metadata so they do
not need to repeatedly load it from SolarNetwork. After you [update][meta-api] metadata, you can
issue this instruction to get the node to pick up the changes.


=== "DatumExpression example"

	```json
	{
		"nodeId": 123,
		"topic": "DatumSourceMetadataClearCache",
		"parameters": [
			{
				"name": "sourceIds",
				"value": "/power/1,/power/2"
			}
		]
	}
	```

=== "DatumExpression example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "DatumSourceMetadataClearCache",
		"params": {
			"expression": "/power/1,/power/2"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `sourceIds` | A comma-delimited list of source IDs to clear cached metadata for. |


## Result

A `Completed` state indicates the cache was cleared successfully.


[meta]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#metadata
[meta-api]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarUser-API#datum-metadata
