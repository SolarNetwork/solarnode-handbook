---
title: Rounded Timestamp
---
# Rounded Timestamp Datum Filter

The [Rounded Timestamp Datum Filter][src] provides a way to round datum timestamps down to clock-aligned values.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Settings

<figure markdown>
  ![Rounded Timestamp filter component settings](../../images/users/datum-filters/rounded-timestamp-filter-settings%402x.png){width=870 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description                                                       |
|:-------------------|:------------------------------------------------------------------|
| Duration           | A duration in milliseconds to round datum timestamps **down** by. See the [note below](#duration) on how the times become clock-aligned. |

## Duration

The resolved timestamp of each output datum will be rounded down based on the duration configured.
For example if `300000` is configured then the resolved timestamps would have times exactly at
5-minute intervals, with _hour:minute_ values like `00:00`, `00:05`, `00:10`, and so on.

Sub-second durations are also supported. For example if `250` is configured then the resolved
timestamps would have times with _second.millisecond_ values like
`00.000`, `00.250`, `00.500`, and so on.

!!! info

	If datum are generated at a rate faster than the configured duration, "duplicate" datum will be
	generated with identical timestamps. If these datum end up in SolarNetwork the most recently
	generated datum will replace any previously generated datum with the same timestamp.

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[dprops]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#datum-property-classifications
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-RoundedTimestamp.md
