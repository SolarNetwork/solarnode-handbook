---
title: Join
---
# Join Datum Filter

The [Join Datum Filter][src] provides a way to merge the properties of multiple datum streams into a
new derived datum stream.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Settings

<figure markdown>
  ![Join filter component settings](../../images/users/datum-filters/join-filter-settings@2x.png){width=882 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting             | Description                                                       |
|:--------------------|:------------------------------------------------------------------|
| Output Source ID    | The source ID of the merged datum stream. [Placeholders][placeholders] are allowed. |
| Coalesce Threshold  | When `2` or more then wait until datum from this many _different_ source IDs have been encountered before generating an output datum. Once a coalesced datum has been generated the tracking of input sources resets and another datum will only be generated after the threshold is met again. If `1` or less, then generate output datum for all input datum. |
| Coalesce Timeout    | When greater than `0` and **Coalesce Threshold** is `2` or more then wait at most this amount of milliseconds for all source IDs to be coalesced before generating a datum out of whatever source IDs have been seen since the last generated datum. If the coalesce threshold is satisfied before this timeout occurs, a datum will be generated as normal. This means a datum will always be generated at at least this frequency, but the datum may not contain properties from all sources. |
| Swallow Input       | If enabled, then filter out input datum after merging. Otherwise leave the input datum as-is. |
| Persist Output     | If enabled then process and persist output datum after merging. Otherwise process output datum but do not persist them. |
| Source Property Mappings |  A list of source IDs with associated property name templates to rename the properties with. Each template must contain a `{p}` parameter which will be replaced by the property names merged from datum encountered with the associated source ID. For example `{p}_s1` would map an input property `watts` to `watts_s1`. |

Use the <kbd>+</kbd> and <kbd>-</kbd> buttons to add/remove source property configurations.

## Source Property Mappings settings

Each source property mapping configuration contains the following settings:

| Setting             | Description                                                       |
|:--------------------|:------------------------------------------------------------------|
| Source ID           | A source ID pattern to apply the associated **Mapping** to. Any _capture groups_ (parts of the pattern between `()` groups) are provided to the **Mapping** template. |
| Mapping             | A property name template with a `{p}` parameter for an input property name to be mapped to a merged (output) property name. Pattern _capture groups_ from **Source ID** are available starting with `{1}`. For example `{p}_s1` would map an input property `watts` to `watts_s1`. |

!!! info "Unmapped properties are copied"

	If a matching source property mapping does not exist for an input datum source ID then the
	property names of that datum are used as-is.

### Source mapping examples

The **Source ID** pattern can define _capture groups_ that will be provided to the **Mapping** template as numbered parameters, starting with `{1}`. For example, assuming an input datum property `watts`, then:

| Datum Source ID | Source ID Pattern | Mapping | Result |
|:----------------|:------------------|:--------|:-------|
| `/power/main`   | `/power/`         | `{p}_main` | `watts_main` |
| `/power/1`      | `/power/(\d+)$`   | `{p}_s{1}` | `watts_s1` |
| `/power/2`      | `/power/(\d+)$`   | `{p}_s{1}` | `watts_s2` |
| `/solar/1`      | `/(\w+)/(\d+)$`   | `{p}_{1}{2}` | `watts_solar1` |

To help visualize property mapping with a more complete example, let's imagine we have some datum
streams being collected and the most recent datum from each look like this:

<table>
<thead>
<tr>
	<th>/meter/1</th>
	<th>/meter/2</th>
	<th>/solar/1</th>
</tr>
<tr>
<td><pre>{
  "watts": 3213,
}</pre></td>
<td><pre>{
  "watts": -842,
}</pre></td>
<td><pre>{
  "watts"  : 4055,
  "current": 16.89583
}</pre></td>
</tr>
</thead>
</table>

Here are some examples of how some source mapping expressions could be defined, including how
multiple mappings can be used at once:

<table>
<thead>
<tr>
	<th>Source ID Patterns</th>
	<th>Mappings</th>
	<th>Result</th>
</tr>
<tr>
<td><code>/(\w+)/(\d+)</code></td>
<td><code>{1}_{p}{2}</code></td>
<td><pre>{
  "power_watts1"  : 3213,
  "power_watts2"  : -842,
  "solar_watts1"  : 4055,
  "solar_current" : 16.89583
}</pre></td>
</tr>
<tr>
<td><ol>
	<li><code>/power/(\d+)</code></li>
	<li><code>/solar/1</code></li>
</ol></td>
<td><ol>
	<li><code>{p}_{1}</code></li>
	<li><code>{p}</code></li>
</ol></td>
<td><pre>{
  "watts_1"  : 3213,
  "watts_2"  : -842,
  "watts"    : 4055,
  "current"  : 16.89583
}</pre></td>
</tr>
</thead>
</table>

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-Join.md
