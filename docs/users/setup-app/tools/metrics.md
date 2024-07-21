SolarNode includes a Metrics page where you can monitor the [metrics](../../metrics.md) collected.

![SolarNode metrics screen](../../../images/users/setup/setup-metrics@2x.png){width=1034}

!!! note

	In SolarNodeOS the [solarnode-app-metrics-harvester][solarnode-app-metrics-harvester]
	package provides a [filter](../../datum-filters/metric-harvester.md) to collect metrics from datum,
	and the [solarnode-app-metrics-db-jdbc][solarnode-app-metrics-db-jdbc]package provides
	database storage for the collected metrics.

The **Latest Metrics** section shows the most-recently collected metric per metric name. This section
will update in real time as metrics are collected.

The **Metric Statistics** section shows you some common metric statistics, aggregated over a date
range of your choosing by filling in the form at the top of the section. You can also download the
statistics as a CSV file using the download button on the right side of the form.

The **Metrics** section shows all the metrics collected, ordered by time from newest to oldest. You
can filter the shown metrics by name or download the metrics as a CSV file using the form at the top
of the section. The pagination links at the bottom let you navigate through all the avilable metrics.

!!! note

	This page displays metric values rounded to at most 3 decimal places. If you export the metrics
	as CSV the values will not be rounded.

[solarnode-app-metrics-db-jdbc]: https://github.com/SolarNetwork/solarnode-os-packages/tree/develop/solarnode-app-metrics-db-jdbc/debian
[solarnode-app-metrics-harvester]: https://github.com/SolarNetwork/solarnode-os-packages/tree/develop/solarnode-app-metrics-harvester/debian
