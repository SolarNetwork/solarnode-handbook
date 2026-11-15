---
title: Datum
---
# Datum endpoints

The datum endpoints provide part of the [SolarQuery API][solarquery-api] for the datum history kept
on SolarNode, so that SolarQuery clients such as the [SolarNetwork Grafana data
source](#grafana-integration) can query SolarNode directly instead of SolarNetwork. They provide
stream metadata, datum (as stored, or aggregated), and readings. Each endpoint is available under
both the `/solarquery/api/v1/sec` path that SolarQuery clients use and the `/api/v1/sec` path of the
rest of the SolarNode REST API. The [node endpoints](nodes.md) provide the node's ID and the sources
in the datum history.

!!! note

	SolarNode keeps a datum history only when a datum historian plugin is installed, such as the
	[Database historian](../../../users/datum-historian.md#database-historian). Without one, these
	endpoints return empty results.

## Differences from SolarQuery

The endpoints accept the same query parameters and return the same response structures as
SolarQuery, with these differences:

 * Only the node's own datum are available. A request that names only other node IDs gets an
   empty result, and other node IDs are ignored when the node's own ID is also requested.
 * Only the `Hour`, `Day`, and `Month` aggregations are supported, and only when the [Database
   historian](../../../users/datum-historian.md#database-historian) has its aggregate setup. Other
   aggregations, and any aggregation without the aggregate setup, are ignored, so datum are returned
   as they were stored.
 * Only the `Difference` reading type is supported.
 * Reading results do not account for meter resets, as SolarNode has nothing like the reset datum
   auxiliary records of SolarNetwork.
 * Datum results have no size limit. A query over a long time range can produce a very large
   response, so use a narrow date range, or the `max` and `offset` parameters.
 * The total result count of a query with `max` is counted only when `withoutTotalResultsCount` is
   `false`, as counting queries all the matching datum, not only those returned.
 * Query parameters that SolarNode does not support, such as `combiningType` or `metadataFilter`,
   are ignored.

## Response formats

Results are provided as JSON, CBOR, or CSV, depending on the `Accept` HTTP header. Datum and reading
stream results use the first of these formats that the `Accept` header allows, taking its media
types in the order given, and CBOR when no `Accept` header is provided. They are compressed with
gzip when the `Accept-Encoding` HTTP header allows it.

An error is returned as an error result, with `success` set to `false`, in the format of the
request and with an HTTP error status.

## Unfiltered history

SolarNode can keep two datum histories: the _filtered_ history of datum after any [datum
filters](../../../users/datum-filters/index.md) are applied, and the _unfiltered_ history of datum
as captured, before any datum filters. The endpoints query the filtered history. To query the
unfiltered history instead, add an `/unfiltered` prefix to the path, for example:

| `GET` | `/unfiltered/solarquery/api/v1/sec/datum/stream/datum` |
|:-|:-|

The request must still be signed for the path **without** the `/unfiltered` prefix, as if the
request were sent through a proxy.

## Grafana integration

The [SolarNetwork Grafana data source][grafana-ds] starting with version 4 can query SolarNode with
these endpoints and the [node endpoints](nodes.md). Use a SolarNode [security
token](../../../users/setup-app/security-tokens.md) for the data source **Token** and **Secret**,
and configure the **Host** and **Proxy** settings to send requests to SolarNode rather than to
SolarNetwork. Use one data source for each datum history:

| Data source | Host | Proxy |
|:------------|:-----|:------|
| Filtered history | `http://solarnode.example` | **No proxy** |
| Unfiltered history | `http://solarnode.example` | `http://solarnode.example/unfiltered` |

Replace `solarnode.example` with the node's host name or IP address, using `http://` as SolarNode
does not serve HTTPS. The unfiltered data source signs its requests for the **Host**, and sends
them to the **Proxy** with its `/unfiltered` path prefix.

!!! warning

	Aggregated results need the aggregate setup of the [Database
	historian](../../../users/datum-historian.md#database-historian). Without it, a Grafana panel
	that shows a long time range, such as a year, gets every datum stored in that range, even when
	the query asks for aggregated results. Grafana reads the entire response into memory.

Queries can use the **List** query type, or the **Reading** query type with the **Difference**
reading type, with a **Combining Type** of **None**. With automatic aggregation the data source asks
for `Hour`, `Day`, or `Month` results as the time range grows. Queries with any other reading type
fail with an error, and queries with any other combining type use SolarQuery endpoints that
SolarNode does not provide, so they fail with an error too.

Live streaming with the data source **MQTT** setting is not supported by SolarNode.

## Query parameters

The endpoints support the following query parameters. The `/datum/stream/meta/node` endpoint uses
only the node, source, and stream ID parameters. The `/datum/stream/reading` endpoint has [some
more](#get-datumstreamreading).

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `nodeId`, `nodeIds` | Number[] | Node IDs. Only the node's own ID matches any datum. |
| `sourceId`, `sourceIds` | String[] | Source IDs, which can be Ant-style patterns, matched case-insensitively. |
| `streamIds` | String[] | Stream IDs, as given in the stream metadata of these endpoints. |
| `startDate` | Date | The minimum datum date, inclusive. |
| `endDate` | Date | The maximum datum date, exclusive. |
| `localStartDate` | Date | The minimum datum date in the node's time zone, inclusive. Used in place of `startDate` and `endDate`. |
| `localEndDate` | Date | The maximum datum date in the node's time zone, exclusive. Used in place of `startDate` and `endDate`. |
| `mostRecent` | Boolean | `true` for only the most recent datum of each stream. |
| `max` | Number | The maximum number of datum to return. |
| `offset` | Number | The number of datum to skip, when `max` is given. |
| `withoutTotalResultsCount` | Boolean | `false` to include the total result count when `max` is given. Defaults to `true`, unlike SolarQuery. |
| `sorts[n].sortKey` | String | A sort key: one of `node`, `source`, or `time` (or `created`). Other keys are ignored. |
| `sorts[n].descending` | Boolean | `true` to sort the matching `sortKey` in descending order. |
| `aggregation` | String | `Hour`, `Day`, or `Month` for aggregated results, when the datum history has aggregates. Other values are ignored. |
| `propertyNames` | String[] | Datum property names to limit datum results to. |

Dates are given in the same forms as SolarQuery, such as `2026-11-01`, `2026-11-01 12:00`, or
`2026-11-01T12:00:00+13:00`. A date without a time zone is in UTC, apart from the `local` dates.
List parameters can be given as a comma-delimited list, or as repeated parameters.

The `local` dates are taken in the time zone of the node, as stored in the datum history, so
`localStartDate=2026-01-01&localEndDate=2026-02-01` gives January in the node's time zone. When
either is given, the `local` dates are used in place of `startDate` and `endDate`, as in SolarQuery.
A `Difference` reading without aggregation, which is one reading over the whole date range, uses
them only when both are given.

## GET `/datum/stream/meta/node`

Get the metadata of the datum streams in the datum history.

| `GET` | `/solarquery/api/v1/sec/datum/stream/meta/node` |
|:-|:-|

The response data is a list of stream metadata objects, as given by the [SolarQuery
endpoint][sq-stream-meta], with the following properties:

| Property | Description |
|:---------|:------------|
| `streamId` | The stream ID. |
| `zone` | The time zone ID of the node. |
| `kind` | Always `n`, for a node datum stream. |
| `objectId` | The node ID. |
| `sourceId` | The source ID. |
| `i` | The instantaneous property names. |
| `a` | The accumulating property names. |
| `s` | The status property names. |

```json title="Example response"
{
	"success": true,
	"data": [
		{
			"streamId": "00000000-0000-8007-ac04-534e00000001",
			"zone": "Pacific/Auckland",
			"kind": "n",
			"objectId": 123,
			"sourceId": "/meter/1",
			"i": ["watts", "voltage"],
			"a": ["wattHours"],
			"s": ["mode"]
		}
	]
}
```

## GET `/datum/stream/datum`

Get datum from the datum history, in the stream form of the [SolarQuery endpoint][sq-stream-datum].

| `GET` | `/solarquery/api/v1/sec/datum/stream/datum` |
|:-|:-|

The response has a `meta` list of stream metadata objects, like those of the
[`/datum/stream/meta/node`](#get-datumstreammetanode) endpoint, and a `data` list of datum. Each
datum is a list of the index of its stream metadata in `meta`, its timestamp as milliseconds since
the epoch, its instantaneous, accumulating, and status property values in the order of the stream
metadata property names, and then any tags. When `max` is given, the response also has
`returnedResultCount` and `startingOffset` properties, and a `totalResultCount` property when
`withoutTotalResultsCount` is `false`.

```json title="Example response"
{
	"success": true,
	"meta": [
		{
			"streamId": "00000000-0000-8007-ac04-534e00000001",
			"zone": "Pacific/Auckland",
			"kind": "n",
			"objectId": 123,
			"sourceId": "/meter/1",
			"i": ["watts", "voltage"],
			"a": ["wattHours"],
			"s": ["mode"]
		}
	],
	"data": [
		[0, 1793487600000, 1230, 240.1, 345000, "auto", "tag1"],
		[0, 1793487660000, 1240, 240.2, 345021, "auto"]
	]
}
```

With an `aggregation` of `Hour`, `Day`, or `Month`, and a datum history with aggregates, each datum
in `data` is instead an aggregate, as in the [SolarQuery aggregate form][sq-stream-datum-response]:
the index of its stream metadata, a list of the aggregate start timestamp and `null`, a list of the
average, count, minimum, and maximum of each instantaneous property, a list of the accumulation,
starting reading, and ending reading of each accumulating property, the most frequent value of each
status property, and then any tags. Hourly aggregates start on the hour in UTC, and daily and
monthly aggregates at midnight in the node's time zone. In CSV, each aggregate property has its own
columns, such as `watts`, `watts_count`, `watts_min`, and `watts_max`, or `wattHours`,
`wattHours_start`, and `wattHours_end`, after `ts_start` and `ts_end` columns.

```json title="Example aggregate response"
{
	"success": true,
	"meta": [
		{
			"streamId": "00000000-0000-8007-ac04-534e00000001",
			"zone": "Pacific/Auckland",
			"kind": "n",
			"objectId": 123,
			"sourceId": "/meter/1",
			"i": ["watts"],
			"a": ["wattHours"],
			"s": ["mode"]
		}
	],
	"data": [
		[0, [1794222000000, null], [1250, 6, 1000, 1500], [600, 50000, 50600], "auto"],
		[0, [1794225600000, null], [1250, 6, 1000, 1500], [600, 50600, 51200], "auto"]
	]
}
```

If an error occurs after SolarNode has started sending the results, the response ends early, with
incomplete results rather than an error result. A client can not parse incomplete JSON, CBOR, or
gzip compressed results, but incomplete CSV results can look complete.

## GET `/datum/stream/reading`

Get readings of the accumulating properties of datum in the datum history, in the stream form of
the [SolarQuery endpoint][sq-stream-reading]: the difference between the readings at the start and
end of a date range, along with those readings.

| `GET` | `/solarquery/api/v1/sec/datum/stream/reading` |
|:-|:-|

The endpoint takes the [query parameters](#query-parameters) of the
[`/datum/stream/datum`](#get-datumstreamdatum) endpoint, and these:

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `readingType` | String | **Required.** The reading type. Only `Difference` is supported. |
| `tolerance` | String | Accepted, but not used by the `Difference` reading type. |
| `aggregation` | String | `Hour`, `Day`, or `Month` for a reading of each aggregate period, when the datum history has aggregates. Other values are ignored. |

Without aggregation, a missing `startDate` or `endDate` is taken to be the current time.

The response has the same structure as that of the `/datum/stream/datum` endpoint, with each
reading in the aggregate form:

 * **Without aggregation**, there is one reading for each stream, over the whole date range. Its
   timestamps are those of the starting and ending readings used, and only the accumulating
   properties have values: the difference, starting reading, and ending reading of each.
 * **With aggregation**, there is one reading for each stream and aggregate period. Its timestamp
   is the start of the period, with `null` for the end. The instantaneous and status properties are
   those of the `/datum/stream/datum` aggregates.

```json title="Example response without aggregation"
{
	"success": true,
	"meta": [
		{
			"streamId": "00000000-0000-8007-ac04-534e00000001",
			"zone": "Pacific/Auckland",
			"kind": "n",
			"objectId": 123,
			"sourceId": "/meter/1",
			"i": ["watts"],
			"a": ["wattHours"],
			"s": ["mode"]
		}
	],
	"data": [
		[0, [1794222000000, 1794308400000], null, [14400, 50000, 64400], null]
	]
}
```

```json title="Example response with Day aggregation"
{
	"success": true,
	"meta": [
		{
			"streamId": "00000000-0000-8007-ac04-534e00000001",
			"zone": "Pacific/Auckland",
			"kind": "n",
			"objectId": 123,
			"sourceId": "/meter/1",
			"i": ["watts"],
			"a": ["wattHours"],
			"s": ["mode"]
		}
	],
	"data": [
		[0, [1794222000000, null], [1250, 144, 1000, 1500], [14400, 50000, 64400], "auto"],
		[0, [1794308400000, null], [1250, 144, 1000, 1500], [14400, 64400, 78800], "manual"]
	]
}
```

The `readingType` parameter is not ignored like other unsupported parameters, because the results
of another reading type would be wrong rather than incomplete. These requests fail:

| Request | Status |
|:--------|:-------|
| No `readingType` | `400` |
| A `readingType` that is not a reading type name, such as `delta` | `422` |
| A `readingType` other than `Difference`, such as `NearestDifference` | `404` |

A request that names only other node IDs gets an empty result whatever its reading type, as
nothing is queried.

[grafana-ds]: https://github.com/SolarNetwork/solarnetwork-grafana-datasource
[solarquery-api]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarQuery-API
[sq-stream-datum]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarQuery-Stream-API#datum-stream-datum-list
[sq-stream-datum-response]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarQuery-Stream-API#datum-stream-datum-list-response
[sq-stream-reading]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarQuery-Stream-API#datum-stream-reading-list
[sq-stream-meta]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarQuery-API#datum-stream-metadata-list
