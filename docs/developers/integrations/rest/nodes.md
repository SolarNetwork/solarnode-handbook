---
title: Nodes
---
# Node endpoints

The node endpoints provide part of the [SolarQuery API][solarquery-api] for the node and the sources
in its datum history, so that SolarQuery clients such as the [SolarNetwork Grafana data
source](datum.md#grafana-integration) can find the datum to query with the [datum
endpoints](datum.md). Each endpoint is available under both the `/solarquery/api/v1/sec` path that
SolarQuery clients use and the `/api/v1/sec` path of the rest of the SolarNode REST API.

Results are provided as JSON, CBOR, or CSV, depending on the `Accept` HTTP header, and as JSON when
no `Accept` header is provided.

## GET `/nodes`

Get the node's ID.

| `GET` | `/solarquery/api/v1/sec/nodes` |
|:-|:-|

No query parameters are supported. The response data is a list with the node's ID, or an empty
list if the node is not associated with SolarNetwork.

```json title="Example response"
{
	"success": true,
	"data": [123]
}
```

## GET `/nodes/sources`

Get the source IDs in the datum history.

| `GET` | `/solarquery/api/v1/sec/nodes/sources` |
|:-|:-|

The following query parameters are supported. Other query parameters, such as dates, are ignored.

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `nodeId`, `nodeIds` | Number[] | Node IDs. Only the node's own ID matches any source. |
| `sourceId`, `sourceIds` | String[] | Source IDs, which can be Ant-style patterns, matched case-insensitively. |
| `streamIds` | String[] | Stream IDs, as given in the [stream metadata](datum.md#get-datumstreammetanode) of the datum endpoints. |

List parameters can be given as a comma-delimited list, or as repeated parameters.

The sources are those of the filtered datum history, or of the unfiltered history when the path
has the `/unfiltered` prefix described in [Unfiltered history](datum.md#unfiltered-history). The
list is empty when SolarNode keeps no datum history, as when no datum historian plugin is
installed.

The response data is a list of objects, ordered by source ID, with the following properties:

| Property | Description |
|:---------|:------------|
| `nodeId` | The node ID. |
| `sourceId` | The source ID. |

```json title="Example response"
{
	"success": true,
	"data": [
		{"nodeId": 123, "sourceId": "/meter/1"},
		{"nodeId": 123, "sourceId": "/meter/2"}
	]
}
```

[solarquery-api]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarQuery-API
