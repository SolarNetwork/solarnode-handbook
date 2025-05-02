# Filter Chain

The **Datum Filter Chain** is a [User Datum Filter][udf] that you configure with a list, or chain,
of _other_ User Datum Filters. When the Filter Chain executes, it executes each of the configured
Datum Filters, in the order defined.  This filter can be used like any other Datum Filter, allowing
multiple filters to be applied in a defined order.

<figure markdown>
  ![Datum Filter Chain diagram](../../images/users/datum-filters/datum-filter-chain.svg#only-light){width=480}
  ![Datum Filter Chain diagram](../../images/users/datum-filters/datum-filter-chain.dark.svg#only-dark){width=480}
  <caption>A Filter Chain acts like an ordered group of Datum Filters</caption>
</figure>

!!! tip

	Some services support configuring only a **single** Datum Filter setting. You can use a Filter
	Chain to apply multiple filters in those services.

## Settings

<figure markdown>
  ![Filter Chain component settings](../../images/users/datum-filters/datum-filter-chain-settings%402x.png){width=736 loading=lazy}
</figure>

In addition to the [Common Settings][datumfilter-common-settings], the following general settings are available:

| Setting | Description |
|:--------|:------------|
| Datum Filters     | The list of **Service Name** values of [User Datum Filter][udf] components to apply to datum. |

[opmodes]: ../op-modes.md
[udf]: ../setup-app/settings/datum-filters.md#user-datum-filters
--8<-- "snippets/users/datum-filters/base-filter-settings-links.md"
