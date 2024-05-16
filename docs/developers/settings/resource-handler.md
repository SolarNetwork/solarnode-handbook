# Setting Resource Handler

The [`net.solarnetwork.node.settings.SettingResourceHandler`][SettingResourceHandler] API defines a
way for a component to import and export files uploaded to SolarNode from external sources.

A component could support importing a file using the [File setting](specifier.md#file). This could
be used, to provide a way of configuring the component from a configuration file, like CSV, JSON,
XML, and so on. Similarly a component could support exporting a file, to generate a configuration
file in another format like CSV, JSON, XML, and so on, from its current settings. For example, the
[Modbus Device Datum Source][modbus-device] does exactly these things: importing and exporting a
custom CSV file to make configuring the component easier.

## Importing

The main part of the `SettingResourceHandler` API for importing files looks like this:

```java
public interface SettingResourceHandler {

	/**
	 * Get a unique, application-wide setting ID.
	 *
	 * <p>
	 * This ID must be unique across all setting resource handlers registered
	 * within the system. Generally the implementation will also be a
	 * {@link net.solarnetwork.settings.SettingSpecifierProvider} for the same
	 * ID.
	 * </p>
	 *
	 * @return unique ID
	 */
	String getSettingUid();

	/**
	 * Apply settings for a specific key from a resource.
	 *
	 * @param settingKey
	 *        the setting key, generally a
	 *        {@link net.solarnetwork.settings.KeyedSettingSpecifier#getKey()}
	 *        value
	 * @param resources
	 *        the resources with the settings to apply
	 * @return any setting values that should be persisted as a result of
	 *         applying the given resources (never {@literal null}
	 * @throws IOException
	 *         if any IO error occurs
	 */
	SettingsUpdates applySettingResources(String settingKey, Iterable<Resource> resources)
			throws IOException;
```

The `getSettingUid()` method overlaps with the [Settings Provider](provider.md) API, and as the comments
note it is typical for a Settings Provider that publishes settings like [File](specifier.md#file) or
[Text Area](specifier.md#text-area) to also implement `SettingResourceHandler`.

The `settingKey` passed to the `applySettingResources()` method identifies the resource(s) being uploaded,
as a single Setting Resource Handler might support multiple resources. For example a Settings Provider might
publish multiple File settings, or File and Text Area settings. The `settingKey` is used to differentiate
between each one.

### Importing example

Imagine a component that publishes a [File](specifier.md#file) setting. A typical implementation of
that component would look like this (this example omits some methods for brevity):

```java
public class MyComponent implements SettingSpecifierProvider,
		SettingResourceHandler {

	private static final Logger log
			= LoggerFactory.getLogger(MyComponent.class);

	/** The resource key to identify the File setting resource. */
	public static final String RESOURCE_KEY_DOCUMENT = "document";

	@Override
	public String getSettingUid() {
		return "com.example.mycomponent";
	}
	@Override
	public List<SettingSpecifier> getSettingSpecifiers() {
		List<SettingSpecifier> results = new ArrayList<>();

		// publish a File setting tied to the RESOURCE_KEY_DOCUMENT key,
		// allowing only text files to be accepted
		results.add(new BasicFileSettingSpecifier(RESOURCE_KEY_DOCUMENT, null,
				new LinkedHashSet<>(asList(".txt", "text/*")), false));

		return results;
	}

	@Override
	public SettingsUpdates applySettingResources(String settingKey,
			Iterable<Resource> resources) throws IOException {
		if ( resources == null ) {
			return null;
		}
		if ( RESOURCE_KEY_DOCUMENT.equals(settingKey) ) {
			for ( Resource r : resources ) {
				// here we would do something useful with the resource... like
				// read into a string and log it
				String s = FileCopyUtils.copyToString(new InputStreamReader(
						r.getInputStream(), StandardCharsets.UTF_8));

				log.info("Got {} resource content: {}", settingKey, s);

				break; // only accept one file
			}
		}
		return null;
	}

}
```

## Exporting

The part of the Setting Resource Handler API that supports exporting setting resources looks like this:

```java
	/**
	 * Get a list of supported setting keys for the
	 * {@link #currentSettingResources(String)} method.
	 *
	 * @return the set of supported keys
	 */
	default Collection<String> supportedCurrentResourceSettingKeys() {
		return Collections.emptyList();
	}

	/**
	 * Get the current setting resources for a specific key.
	 *
	 * @param settingKey
	 *        the setting key, generally a
	 *        {@link net.solarnetwork.settings.KeyedSettingSpecifier#getKey()}
	 *        value
	 * @return the resources, never {@literal null}
	 */
	Iterable<Resource> currentSettingResources(String settingKey);

```

The `supportedCurrentResourceSettingKeys()` method returns a set of resource keys the component supports
for exporting. The `currentSettingResources()` method returns the resources to export for a given key.

The SolarNode UI shows a form menu with all the available resources for all components that support
the `SettingResourceHandler` API, and lets the user to download them:

![Resource export UI in SolarNode](../../images/developers/settings/setting-resource-export-ui.png){width=482}

### Exporting example

Here is an example of a component that supports exporting a CSV file resource based on
the component's current configuration:

```java
public class MyComponent implements SettingSpecifierProvider,
		SettingResourceHandler {

	/** The setting resource key for a CSV configuration file. */
	public static final String RESOURCE_KEY_CSV_CONFIG = "csvConfig";

	private int max = 1;
	private boolean enabled = true;

	@Override
	public Collection<String> supportedCurrentResourceSettingKeys() {
		return Collections.singletonList(RESOURCE_KEY_CSV_CONFIG);
	}

	@Override
	public Iterable<Resource> currentSettingResources(String settingKey) {
		if ( !RESOURCE_KEY_CSV_CONFIG.equals(settingKey) ) {
			return null;
		}

		StringBuilder buf = new StringBuilder();
		buf.append("max,enabled\r\n");
		buf.append(max).append(',').append(enabled).append("\r\n");

		return Collections.singleton(new ByteArrayResource(
				buf.toString().getBytes(UTF_8), "My Component CSV Config") {

					@Override
					public String getFilename() {
						return "my-component-config.csv";
					}

				});
	}
}
```

[modbus-device]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.datum.modbus
[SettingResourceHandler]: https://javadoc.io/doc/net.solarnetwork.node/net.solarnetwork.node/latest/net/solarnetwork/node/settings/SettingResourceHandler.html
