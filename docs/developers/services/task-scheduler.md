# Task Scheduler

To support asynchronous task scheduling, SolarNode provides a Spring [TaskScheduler][TaskScheduler]
service to plugins.

!!! tip "The Job Scheduler"

	For user-configurable scheduled tasks, check out the [Job Scheduler](job-scheduler.md)
	service.

To make use of any of this service from a plugin using [OSGi Blueprint](../osgi/blueprint.md) you can
declare a reference like this:

```xml
<reference id="taskScheduler" interface="org.springframework.scheduling.TaskScheduler"
		filter="(function=node)"/>
```

## Configuration

The Task Scheduler supports the following [configuration properties](../../users/configuration.md) in the `net.solarnetwork.node.core` namespace:

<div markdown="1" class="props-explicit-col-widths">
| Property | Default | Description |
|:---------|:--------|:------------|
| `jobScheduler.poolSize` | 10 | The number of threads to maintain in the job scheduler, and thus the maximum number of jobs that can run simultaneously. Must be set to 1 or higher. |
| `scheduler.startupDelay` | 180 | A delay in seconds after creating the job scheduler to start triggering jobs. This can be useful to give the application time to completely initialize before starting to run jobs. |
</div>

For example, to change the thread pool size to 20 and shorten the startup delay to 30 seconds, create an
`/etc/solarnode/services/net.solarnetwork.node.core.cfg` file with the following content:

```properties
jobScheduler.poolSize = 20
scheduler.startupDelay = 30
```

[TaskScheduler]: https://docs.spring.io/spring-framework/docs/6.2.0/javadoc-api/org/springframework/scheduling/TaskScheduler.html
