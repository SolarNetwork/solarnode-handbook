---
title: Property
---
# Property Datum Filter

The [Property Datum Filter][src] provides a way to remove **properties** of datum. This can help if
some component generates properties that you don't actually need to use.

For example you might have a plugin that collects data from an AC power meter that capture power,
energy, quality, and other properties each time a sample is taken. If you are only interested in
capturing the power and energy properties you could use this component to remove all the others.

This component can also throttle individual properties over time, so that individual properties are
posted less frequently than the rate the whole datum it is a part of is sampled at. For example a
plugin for an AC power meter might collect datum once per minute, and you want to collect the energy
properties of the datum every minute but the quality properties only once every 10 minutes.

The general idea for filtering properties is to configure rules that define which datum **sources**
you want to filter, along with a list of **properties** to _include_ and/or a list to _exclude_. All
matching is done using regular expressions, which can help make your rules concise.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Settings

<figure markdown>
  ![Property filter component settings](../../images/users/datum-filters/property-filter-settings%402x.png){width=610 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description                                                       |
|:-------------------|:------------------------------------------------------------------|
| Property Includes  | A list of property names to include, removing all others.  This is a list of case-insensitive [patterns][regex] to match against datum **property names**. If any inclusion patterns are configured then **only** properties matching one of these patterns will be included in datum. Any property name that does not match one of these patterns will be removed. |
| Property Excludes  | A list of property names to exclude. This is a list of case-insensitive [patterns][regex] to match against datum **property names**. If any exclusion expressions are configured then **any** property that matches one of these expressions will be removed. Exclusion epxressions are processed **after** inclusion expressions when both are configured.|

Use the <kbd>+</kbd> and <kbd>-</kbd> buttons to add/remove property include/exclude patterns.

Each property inclusion setting contains the following settings:

| Setting            | Description                                                       |
|:-------------------|:------------------------------------------------------------------|
| Name               | The property name pattern to include.                             |
| Limit Seconds      | A throttle limit, in seconds, to apply to included properties. The minimum number of seconds to limit properties that match the configured property inclusion pattern. If properties are produced faster than this rate, they will be filtered out. Leave empty (or `0`) for no throttling. |

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-Property.md
