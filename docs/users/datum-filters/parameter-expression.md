---
title: Parameter Expression
---
# Parameter Expression Datum Filter

The [Parameter Expression Datum Filter][src] provides a way to generate filter parameters by
evaluating expressions against existing properties. The generated parameters will be available to
any further datum filters in the same filter chain.

!!! tip

	Parameters are useful as temporary variables that you want to use during datum processing but do
	not want to include as datum properties that get posted to SolarNet.

This filter is provided by the [Standard Datum Filters][sdf] plugin.

## Settings

<figure markdown>
  ![Parameter Expression filter component settings](../../images/users/datum-filters/parameter-filter-settings%402x.png){width=629 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting            | Description                                                       |
|:-------------------|:------------------------------------------------------------------|
| Expressions        |  A list of expression configurations that are evaluated to derive parameter values from other property values. |

Use the <kbd>+</kbd> and <kbd>-</kbd> buttons to add/remove expression configurations.

## Expression settings

Each expression configuration contains the following settings:

| Setting             | Description                                                       |
|:--------------------|:------------------------------------------------------------------|
| Parameter           | The filter parameter name to store the expression result in. |
| Expression          | The expression to evaluate. See [below](#expressions) for more info. |
| Expression Language | The [expression language][expr-lang] to write **Expression** in. |

## Expressions

See the [Expressions](../expressions.md) section for general expressions reference. This filter
supports Datum Expressions that lets you treat all datum properties, and filter parameters, as
expression variables directly.

--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
[placeholders]: ../placeholders.md
[sdf]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/
[src]: https://github.com/SolarNetwork/solarnetwork-node/blob/develop/net.solarnetwork.node.datum.filter.standard/README-Parameter.md

