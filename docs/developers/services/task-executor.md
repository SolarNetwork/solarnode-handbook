# Task Executor

To support asynchronous task execution, SolarNode makes several thread-pool based services available
to plugins:

* A `java.util.concurrent.Executor` service for standard `Runnable` task execution
* A Spring [`TaskExecutor`][TaskExecutor] service for `Runnable` task execution
* A Spring [`AsyncTaskExecutor`][AsyncTaskExecutor] service for both `Runnable` and `Callable` task execution
* A Spring [`AsyncListenableTaskExecutor`][AsyncListenableTaskExecutor] service for both `Runnable`
  and `Callable` task execution that supports the
  `org.springframework.util.concurrent.ListenableFuture` API

!!! tip "Need to schedule tasks?"

	See the [Task Scheduler](task-scheduler.md) page for information on scheduling simple tasks,
	or the [Job Scheduler](job-scheduler.md) page for information on scheduling managed jobs.

To make use of any of these services from a plugin using [OSGi Blueprint](../osgi/blueprint.md) you can
declare a reference to them like this:

```xml
<reference id="taskExecutor" interface="java.util.concurrent.Executor"
		filter="(function=node)"/>

<reference id="taskExecutor" interface="org.springframework.core.task.TaskExecutor"
		filter="(function=node)"/>

<reference id="taskExecutor" interface="org.springframework.core.task.AsyncTaskExecutor"
		filter="(function=node)"/>

<reference id="taskExecutor" interface="org.springframework.core.task.AsyncListenableTaskExecutor"
		filter="(function=node)"/>
```

## Thread pool configuration

This thread pool is configured as a fixed-size pool with the number of threads set to the number of
CPU cores detected at runtime, plus one. For example on a Raspberry Pi 4 there are 4 CPU cores so
the thread pool would be configured with 5 threads.

[AsyncListenableTaskExecutor]: https://docs.spring.io/spring-framework/docs/6.2.0/javadoc-api/org/springframework/core/task/AsyncListenableTaskExecutor.html
[AsyncTaskExecutor]: https://docs.spring.io/spring-framework/docs/6.2.0/javadoc-api/org/springframework/core/task/AsyncTaskExecutor.html
[TaskExecutor]: https://docs.spring.io/spring-framework/docs/6.2.0/javadoc-api/org/springframework/core/task/TaskExecutor.html
