# Closeable Service

A plugin can publish a `net.solarnetwork.service.CloseableService` and SolarNode will invoke the
`closeService()` method on it when that service is destroyed. This can be useful in some situations,
to make sure resources are freed when a service is no longer needed.

Blueprint does provide the `destroy-method` [stop hook](../osgi/blueprint.md#startstop-hooks) that can be
used in many situations, however Blueprint does not allow this in all cases. For example a `<bean>`
nested within a `<service>` element does not allow a `destroy-method`:

```xml
<service interface="com.example.MyService">
	<!-- destroy-method not allowed here: -->
	<bean class="com.example.MyComponent"/>
</service>
```

If `MyComponent` also implemented `CloseableService` then we can achieve the desired stop hook like
this:

```xml
<service>
	<interfaces>
		<value>com.example.MyService</value>
		<value>net.solarnetwork.service.CloseableService</value>
	</interfaces>
	<bean class="com.example.MyComponent"/>
</service>
```

!!! note

	Note that the above example `CloseableService` is not strictly needed, as the same effect could
	be acheived by un-nesting the `<bean>` from the `<service>` element, like this:

	```xml
	<bean id="myComponent" class="com.example.MyComponent" destroy-method="close"/>
	<service ref="myComponent" interface="com.example.MyService"/>
	```

	There are situations where un-nesting is not possible, which is where `CloseableService` can be
	helpful.
