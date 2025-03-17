---
title: Downsample
---
# Downsample Datum Filter

The [Downsample Datum Filter][src] provides a way to down-sample higher-frequency datum samples into
lower-frequency (averaged) datum samples. The filter will collect a configurable number of samples
and then generate a down-sampled sample where an average of each collected instantaneous property is
included. In addition minimum and maximum values of each averaged property are added.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Downsample property handling

The method of deriving a downsample property value depends on the [property classification][dprops]
of each property.

| Class. | Description | Method |
|:-------|:------------|:-------|
| `i`    | instantaneous | The **average** of the collected values is used. Optional _minimum_ and _maximum_ property values can also be generated. |
| `a`    | accumulating  | The **most recent** (last seen) of the collected values is used. |
| `s`    | status        | The **most recent** (last seen) of the collected values is used. |

## Settings

<figure markdown>
  ![Downsample filter component settings](../../images/users/datum-filters/downsample-filter-settings%402x.png){width=934 loading=lazy}
</figure>

| Setting            | Description                                                       |
|:-------------------|:------------------------------------------------------------------|
--8<-- "snippets/users/datum-filters/base-filter-settings.md"
| Sample Count          | The number of samples to average over. |
| Sample Duration       | A duration in seconds to collect samples within before generating an averaged output. Overrides the **Sample Count** setting. See the [note below](#sample-duration) on how the sample times become clock-aligned. |
| Decimal Scale         | A maximum number of digits after the decimal point to round to. Set to`0` to round to whole numbers. |
| Property Excludes     | A list of property names to exclude. |
| Min Property Template | A string format to use for computed minimum property values. Use `%s` as the placeholder for the original property name, e.g. `%s_min`. |
| Max Property Template | A string format to use for computed maximum property values. Use `%s` as the placeholder for the original property name, e.g. `%s_max`. |

### Sample Duration

When this setting is configured, the resolved timestamp of each downsampled datum will be
clock-aligned based on the period configured. For example if `300` is configured, then
the resolved timestamps would have times exactly at `00:00`, `00:05`, `00:10` and so on.

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[dprops]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#datum-property-classifications
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-Downsample.md
