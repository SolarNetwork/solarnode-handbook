# Blueprint

SolarNode supports the OSGi Blueprint Container Specification so plugins can declare their service
dependencies and register their services by way of an XML file deployed with the plugin. If you are
familiar with the [Spring Framework's XML configuration][spring-xml-config], you will find Blueprint
very similar. SolarNode uses the [Eclipse Gemini][eclipse-gemini-blueprint] implementation of the
Blueprint specification, which is directly derived from Spring Framework.

!!! note
	This guide will not document the full Blueprint XML syntax. Rather, it will attempt to showcase the
	most common parts used in SolarNode. Refer to the [Blueprint Container
	Specification][osgi-blueprint] for full details of the specification.

## Example

Imagine you are working on a plugin and have a `com.example.Greeter` interface you would like to
register as a service for other plugins to use, and an implementation of that service in
 `com.example.HelloGreeter` that relies on the [Placeholder
 Service](../services/placeholder-service.md) provided by SolarNode:

=== "Greeter service"

	```java
	package com.example;
	public interface Greeter {

		/**
		 * Greet something with a given name.
		 * @param name the name to greet
		 * @return the greeting
		 */
		String greet(String name);

	}
	```

=== "HelloGreeter implementation"

	```java
	package com.example;
	import net.solarnetwork.node.service.PlaceholderService;
	public class HelloGreeter implements Greeter {

		private final PlaceholderService placeholderService;

		public HelloGreeter(PlaceholderService placeholderService) {
			super();
			this.placeholderService = placeholderService;
		}

		@Override
		public String greet(String name) {
			return placeholderService.resolvePlaceholders(
				String.format("Hello %s, from {myName}.", name),
				null);
		}
	}
	```

Assuming the `PlaceholderService` will resolve `{name}` to `Office Node`, we would expect the `greet()` method to
run like this:

```java
Greeter greeter = resolveGreeterService();
String result = greeter.greet("Joe");
// result is "Hello Joe, from Office Node."
```

In the plugin we then need to:

 1. Obtain a `net.solarnetwork.node.service.PlaceholderService` to pass to the
    `HelloGreeter(PlaceholderService)` constructor
 2. Register  the `HelloGreeter` comopnent as a `com.example.Greeter` service in the SolarNode
    platform

Here is an example Blueprint XML document that does both:

```xml title="Blueprint XML example"
<?xml version="1.0" encoding="UTF-8"?>
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
		http://www.osgi.org/xmlns/blueprint/v1.0.0
		https://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">

	<!-- Declare a reference (lookup) to the PlaceholderService -->
	<reference id="placeholderService"
		interface="net.solarnetwork.node.service.PlaceholderService"/>

	<service interface="com.example.Greeter">
		<bean class="com.example.HelloGreeter">
			<argument ref="placeholderService">
		</bean>
	</service>

</blueprint>
```

## Blueprint XML Resources

Blueprint XML documents are added to a plugin's `OSGI-INF/blueprint` classpath location. A plugin
can provide any number of Blueprint XML documents there, but often a single file is sufficient and
a common convention in SolarNode is to name it `module.xml`.

## XML Syntax


A minimal Blueprint XML file is structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
		http://www.osgi.org/xmlns/blueprint/v1.0.0
		https://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">

	<!-- Plugin components configured here -->

</blueprint>
```

### Service References

To make use of services registered by SolarNode plugins, you declare a _reference_ to that service
so you may refer to it elsewhere within the Blueprint XML. For example, imagine you wanted to use
the [Placeholder Service](../services/placeholder-service.md) in your component. You would obtain a
reference to that like this:

```xml
<reference id="placeholderService"
	interface="net.solarnetwork.node.service.PlaceholderService"/>
```

The `id` attribute allows you to refer to this service elsewhere in your Blueprint XML, while
`interface` declares the fully-qualified Java interface of the service you want to use.

### Components

Components in Blueprint are Java classes you would like instantiated when your plugin starts. They are
declared using a `<bean>` element in Blueprint XML. You can assign each component a unique identifier using
an `id` attribute, and then you can refer to that component in other components.

Imagine an example component class `com.example.MyComponent`:

```java
package com.example;

import net.solarnetwork.node.service.PlaceholderService;

public class MyComponent {

	private final PlaceholderService placeholderService;
	private int minimum;

	public MyComponent(PlaceholderService placeholderService) {
		super();
		this.placeholderService = placeholderService;
	}

	public String go() {
		return PlaceholderService.resolvePlaceholders(placeholderService,
				"{building}/temp", null);
	}

	public int getMinimum() {
		return minimum;
	}

	public void setMinimum(int minimum) {
		this.minimum = minimum;
	}
}
```

Here is how that component could be declared in Blueprint:

```xml
<reference id="placeholderService"
	interface="net.solarnetwork.node.service.PlaceholderService"/>

<bean id="myComponent" class="com.example.MyComponent">
	<argument ref="placeholderService">
	<property name="minimum" value="10"/>
</bean>
```

#### Constructor Arguments

If your component requires any constructor arguments, they can be specified with nested `<argument>`
elements in Blueprint. The `<argument>` value can be specified as a reference to another component
using a `ref` attribute whose value is the `id` of that component, or as a literal value using a
`value` attribute.

For example:

```xml
<bean id="myComponent" class="com.example.MyComponent">
	<argument ref="placeholderService">
	<argument value="10">
```

#### Property Accessors

You can configure mutable class properties on a component with nested `<property name="">` elements
in Blueprint. A _mutable property_ is a Java setter method. For example an `int` property `minimum`
would be associated with a Java setter method `#!java public void setMinimum(int value)`.

The `<property>` value can be specified as a reference to another component
using a `ref` attribute whose value is the `id` of that component, or as a literal value using a
`value` attribute.

For example:


```xml
<bean id="myComponent" class="com.example.MyComponent">
	<property name="placeholderService" ref="placeholderService">
	<argument name="minimum" value="10">
```

#### Start/Stop Hooks

Blueprint can invoke a method on your component when it has finished instantiating and configuring
the object (when the plugin starts), and another when it destroys the instance (when the plugin is
stopped). You simply provide the name of the method you would like Blueprint to call in the
`init-method` and `destroy-method` attributes of the `<bean>` element. For example:

```xml
<bean id="myComponent" class="com.example.MyComponent"
	init-method="startup"
	destroy-method="shutdown">
```

### Service Registration

You can make any component available to other plugins by _registering_ the component with a
`<service>` element that declares what interface(s) your component provides. Once registered, other
plugins can make use of your component, for example by declaring a `<referenece>` to your component
class in _their_ Blueprint XML.

!!! note
	You can only register Java _interfaces_ as services, not _classes_.

For example, imagine a `com.example.Startable` interface like this:

```java
package com.example;
public interface Startable {
	/**
	 * Start!
	 * @return the result
	 */
	String go();
}
```

We could implement that interface in the `MyComponent` class, like this:

```java
package com.example;

public class MyComponent implements Startable {

	@Override
	public String go() {
		return "Gone!";
	}
}
```

We can register `MyComponent` as a `Startable` service using a `<service>` element like this in
Blueprint:

=== "Direct service component"

	```xml
	<service interface="com.example.Startable">
		<!-- The service implementation is nested directly within -->
		<bean class="com.example.MyComponent"/>
	</service>
	```

=== "Indirect service component"

	```xml
	<!-- The service implementation is referenced indirectly... -->
	<service ref="myComponent" interface="com.example.Startable"/>

	<!-- ... to a bean with a matching id attribute -->
	<bean id="myComponent" class="com.example.MyComponent"/>
	```

#### Multiple Service Interfaces

You can advertise any number of service interfaces that your component supports, by nesting an `<interfaces>`
element within the `<service>` element, in place of the `interface` attribute. For example:

```xml
<service ref="myComponent">
	<interfaces>
		<value>com.example.Startable</value>
		<value>com.example.Stopable</value>
	</interfaces>
</service>
```

#### Export service packages

For a registered service to be of any use to another plugin, the package the service is defined in
_must_ be [exported](manifest.md#package-exports) by the plugin hosting that package. That is
because the plugin wishing to add a [reference](#service-references) to the service will need to
[import](manifest.md#package-dependencies) the package in order to use it.

For example, the plugin that hosts the `com.example.service.MyService` service would need a
[manifest](manifest.md) file that includes an [`Export-Package`](manifest.md#package-exports)
attribute similar to:

```properties
Export-Package: com.example.service;version="1.0.0"
```

[eclipse-gemini-blueprint]: https://eclipse.org/gemini/blueprint/
[osgi-blueprint]: https://docs.osgi.org/specification/osgi.cmpn/7.0.0/service.blueprint.html
[spring-xml-config]: https://docs.spring.io/spring-framework/reference/6.2/core/beans/basics.html#beans-factory-xml
