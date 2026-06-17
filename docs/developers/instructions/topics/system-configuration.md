---
title: SystemConfiguration
---
# `SystemConfiguration` topic

This instruction requests the node to return the configuration for a service. The nature
of the returned configuration is service-specific.

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `service` | The ID of the service to get the configuration of. |

Each service implementation can support additional parameters. See [below](#standard-system-configuration-services) for more details.

## Result

A `Completed` instruction will include a `result` result parameter with the configuration data in a service-specific form.
See the following section for more details.

## Standard system configuration services

The following `service` values represent some standard services supported in SolarNode:

| Service | Description |
|:--------|:------------|
| [`net.solarnetwork.node.controls`](#controls-service) | Will return a comma-delimited list of all available control IDs. |
| [`net.solarnetwork.node.packages`](#packages-service) | Will return a JSON array of package information. |
| [`net.solarnetwork.node.settings`](#settings-service) | Will return a JSON string of runtime settings. |

### Controls service

The `net.solarnetwork.node.controls` service can return a list of control IDs available in SolarNode.

=== "Controls example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfiguration",
		"parameters": [
			{"name": "service", "value": "net.solarnetwork.node.controls"},
			{"name": "filter", "value": "/switch/*"}
		]
	}
	```

=== "Controls example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfiguration",
		"params": {
			"service": "net.solarnetwork.node.controls",
			"filter": "/switch/*"
		}
	}
	```

#### Controls parameters

The additional parameters supported by this instruction are:

| Parameter | Description |
|:----------|:------------|
| `filter`  | An optional [wildcard pattern][wildcards] to restrict the returned control IDs to those that match the pattern. |

#### Controls result

The result is returned as a comma-delimited list of IDs on a `result` result parameter.

=== "Controls result"

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 21:39:09.154986Z",
			"topic": "SystemConfiguration",
			"instructionDate": "2024-06-11 21:39:09.154867Z",
			"statusDate": "2024-06-11 21:39:09.649036Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "net.solarnetwork.node.packages"
				},
				{
					"name": "filter",
					"value": "/switch/*"
				}
			],
			"nodeId": 123,
			"resultParameters": {
				"result": "/switch/1,/switch/2"
			}
		}
	}
	```

### Packages service

The `net.solarnetwork.node.packages` service can return information about the software packages
installed or available to install. The result is returned as an array of package objects on the
`result` result parameter.

#### Packages parameters

The additional parameters supported by this instruction are:

| Parameter | Description |
|:----------|:------------|
| `compress` | This optional parameter, when `true`, requests the result to be return as gzip-compressed JSON and encoded as a Base64 string. This can be helpful when working with complex components with many configurable settings, where the result can be large. If the compressed result is larger than the uncompressed JSON then the original results will be returned instead, unless this parameter is set to `force`. |
| `filter`  | An optional parameter for a regular expression to match against package names. Only package names that match the pattern will be returned in the result. If unspecified, a default pattern of `(^sn-\|solarnode)` will be used (match packages whose names start with `sn-` or contain `solarnode`). |
| `status` | An optional parameter that can be one of `Installed` (include only installed packages, the default if not specified), `Available` (include only pacakges that are not currently installed), or `All` (include both installed and available packages). |

#### Packages result

The `result` result parameter will be a JSON array of package objects. Each package object contains
the following properties:

| Property | Type | Description |
|:---------|:-----|:------------|
| `name`      | string | The name of the package. |
| `version`   | string | The version of the package. |
| `installed` | boolean | `true` if the package is currently installed. |

An example instruction after completing looks like this as JSON:

=== "List packages result"

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 21:39:09.154986Z",
			"topic": "SystemConfiguration",
			"instructionDate": "2024-06-11 21:39:09.154867Z",
			"statusDate": "2024-06-11 21:39:09.649036Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "net.solarnetwork.node.packages"
				}
			],
			"nodeId": 123,
			"resultParameters": {
				"result": [
					{
					"name": "sn-solarpkg",
					"version": "1.3.0-1",
					"installed": true
					},
					{
					"name": "sn-system",
					"version": "1.7.0-1",
					"installed": true
					},
					{
					"name": "solarnode-app-core",
					"version": "3.20.0-1",
					"installed": true
					},
					{
					"name": "solarnode-app-db-h2",
					"version": "2.0.1-1",
					"installed": true
					},
					{
					"name": "solarnode-app-solarflux",
					"version": "1.1.0-1",
					"installed": true
					},
					{
					"name": "solarnode-base",
					"version": "4.1.1-1",
					"installed": true
					}
				]
			}
		}
	}
	```

=== "Compressed result"

	If the `compress` parameter is used to provide compressed results, the completed instruction
	would look like this:

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 22:22:23.335316Z",
			"topic": "SystemConfiguration",
			"instructionDate": "2024-06-11 22:22:23.335258Z",
			"statusDate": "2024-06-11 22:22:23.87532Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "net.solarnetwork.node.packages"
				},
				{
					"name": "compress",
					"value": "true"
				}
			],
			"nodeId": 123,
			"resultParameters": {
				"result": "H4sIAAAAAAAA/6WW227jIBRF/8XPxXJtVzPqr4zmATs4JcVAOZBJNOq/F9t5GE7oAI4i5b4XsM+NX38rSWdWvVaGnZU4MwJKUCPVgRGqNRmVYdVTdWYGuJL+b139s27Is/+OS7BUCHaoXq1x7PPpP6iBQoh5rvskBiSZB62EQMqXuk0r5WTpIBggrX+ktQr8j/ZO2ZA2pdQcqdqcU2pOHAwEnNbK4HWzCOZiL5H9JnVrhAY6vjuN5I2XN1ly/X5E2i57aYC3yLpdUnsFy2ak/JGz6h8+4RD16cAGJeHtkgxHqUnnVaqu2iZ9gIBxoNbNExfWQ8qTJkQN5K0NGG3xkbiKW5ORiHHO7aU/oY3tAM6DwxaVun2jkNP2HKm4UtMXojrgnXW7zrdyiOSTvZITjMixtu6KiR82DGS/yzFPIT6G9vo4C5jhVCCvdnMiLpWyhLooicdaKSSamWWFF0miBfJSBInOvNLDALNOx3psMWj5NAm3Y6iFHCdBs/EBg+9uLn3OHSKQ+2Y/z0oCEVQeO1zsJV1jpaFGXWbuSjj5gQ9efI/JnYMb5ttOWBKmDbUV5WIUbvZN3Zex7tvNjrNtnfTWVnGr8PmTccUJgYbR0fLlUmz92/mRabTyQBsuj9X+DvYPxdft6AxHrn2P+/0FUb4oXTYMAAA="
			}
		}
	}
	```


### Settings service

The `net.solarnetwork.node.settings` service can return the configuration (settings) for any
configurable component and component factory available in the SolarNode runtime. This can be used
to discover the configuration of a node. The result is returned as a JSON string on the `result`
result parameter.

#### Settings parameters

The additional parameters supported by this instruction are:

| Parameter | Description |
|:----------|:------------|
| `uid`      | This **required** parameter represents the unique ID of the component you are interested in. |
| `id`       | This optional parameter represents the instance ID of the factory component you are interested in, implying that `uid` is the ID of a factory component. |
| `compress` | This optional parameter, when `true`, requests the JSON result to be gzip-compressed and encoded as a Base64 string. This can be helpful when working with complex components with many configurable settings, where the JSON result can be large. |
| `spec`     | This optional parameter, when `true`, causes [specification objects](#specification-mode) to be returned instead of setting values. This can be used by tools to understand what settings are available. |

The presence of the `id` parameter, along with a special `*` value for both the `uid` and `id`
parameters, determines the result output according to the following table:

| `uid` | `id` | Operation |
|:------|:-----|:----------|
| `*` |  | [List providers](#list-providers) |
| _{settingUid}_ |  | [List settings](#list-settings) |
| `*` | `*` | [List factories](#list-factories) |
| _{factoryUid}_ | `*` | [List factory instances](#list-factory-instances) |
| _{factoryUid}_ | _{instanceId}_ | [List factory instance settings](#list-factory-instance-settings) |

#### List providers

Pass a `uid` value of `*` to generate a JSON array of objects, each object representing a setting
component. Each component object contains the following properties:

| Property | Description |
|:---------|:------------|
| `id`     | The provider ID. |
| `title`  | A description of the provider. |

=== "List providers result example"

	```json
	{
		"result":"[{\"id\":\"net.solarnetwork.node.backup.DefaultBackupManager\",\"title\":\"Backup Manager\"},{\"id\":\"net.solarnetwork.node.backup.s3.S3BackupService\",\"title\":\"S3 Backup Service\"},{\"id\":\"net.solarnetwork.node.control.bacnet.csv\",\"title\":\"BACnet Control CSV Configurer\"},{\"id\":\"net.solarnetwork.node.control.modbus.csv\",\"title\":\"Modbus Control CSV Configurer\"}]"
	}
	```

=== "Result JSON"

	Parsing the `result` value as JSON you can see the result structure more clearly:

	```json
	[
		{"id":"net.solarnetwork.node.backup.DefaultBackupManager","title":"Backup Manager"},
		{"id":"net.solarnetwork.node.backup.s3.S3BackupService","title":"S3 Backup Service"},
		{"id":"net.solarnetwork.node.control.bacnet.csv","title":"BACnet Control CSV Configurer"},
		{"id":"net.solarnetwork.node.control.modbus.csv","title":"Modbus Control CSV Configurer"}
	]
	```

#### List settings

Pass a `uid` value of the non-factory setting component ID to generate a JSON array of setting
objects for that component. Each setting object contains the following properties:

| Property | Description |
|:---------|:------------|
| `key`     | The setting key. |
| `value`   | The setting value. |
| `default` | If present, a boolean value that, when `true`, indicates the `value` is a node-provided default, not a user-configured value. |

=== "List settings result example"

	```json
	{
		"result":"[{\"key\":\"job.instructionAcknowledgementUploader.cron\",\"value\":\"35 * * * * ?\",\"default\":true},{\"key\":\"job.instructionCleaner.cron\",\"value\":\"50 * * * * ?\",\"default\":true},{\"key\":\"job.instructionExecution.cron\",\"value\":\"10 * * * * ?\"}]"
	}
	```

=== "Result JSON"

	Parsing the `result` value as JSON you can see the result structure more clearly:

	```json
	[
		{"key":"job.instructionAcknowledgementUploader.cron","value":"35 * * * * ?","default":true},
		{"key":"job.instructionCleaner.cron","value":"50 * * * * ?","default":true},
		{"key":"job.instructionExecution.cron","value":"10 * * * * ?"}
	]
	```

!!! note

	If the `spec` instruction parameter is `true`, then [specification objects](#specification-mode)
	will be returned instead of setting objects.

#### List factories

Pass a `uid` value of `*` **and** an `id` value of `*` to generate a JSON array of objects, each
object representing a setting factory component. Each factory component has the same properties as
shown in the [List providers](#list-providers) section.

=== "List factories result example"

	```json
	{
		"result":"[{\"id\":\"net.solarnetwork.node.control.bacnet\",\"title\":\"BACnet Control\"},{\"id\":\"net.solarnetwork.node.control.gpiod\",\"title\":\"GPIO Control\"},{\"id\":\"net.solarnetwork.node.control.modbus\",\"title\":\"Modbus Control\"},{\"id\":\"net.solarnetwork.node.datum.bacnet\",\"title\":\"BACnet Device\"}]"
	}
	```

=== "Result JSON"

	Parsing the `result` value as JSON you can see the result structure more clearly:

	```json
	[
		{"id":"net.solarnetwork.node.control.bacnet","title":"BACnet Control"},
		{"id":"net.solarnetwork.node.control.gpiod","title":"GPIO Control"},
		{"id":"net.solarnetwork.node.control.modbus","title":"Modbus Control"},
		{"id":"net.solarnetwork.node.datum.bacnet","title":"BACnet Device"}
	]
	```

#### List factory instances

Pass a `uid` value of a factory component ID **and** an `id` value of `*` to generate a JSON array
of strings, each representing a setting factory component instance ID for the given factory.

=== "List factory instances result example"

	```json
	{
		"result":"[\"Meter\",\"Inverter\",\"Pyrometer\",\"Weather\"]"
	}
	```

=== "Result JSON"

	Parsing the `result` value as JSON you can see the result structure more clearly:

	```json
	["Meter","Inverter","Pyrometer","Weather"]
	```

#### List factory instance settings

Pass a `uid` value of a factory component ID **and** an `id` value of an associated instance ID to
generate a JSON array of setting objects for the given factory instance. Each setting object has the
same properties as shown in the [List settings](#list-settings) section.

=== "List factory instance settings result example"

	```json
	{
		"result":"[{\"key\":\"schedule\",\"value\":\"6000\"},{\"key\":\"jobService.datumDataSource.uid\",\"value\":\"\",\"default\":true},{\"key\":\"jobService.datumDataSource.groupUid\",\"value\":\"\",\"default\":true},{\"key\":\"jobService.datumDataSource.publishDeviceInfoMetadata\",\"value\":true,\"default\":true},{\"key\":\"jobService.datumDataSource.sourceId\",\"value\":\"/power/1\"},{\"key\":\"jobService.datumDataSource.voltage\",\"value\":\"230.0\",\"default\":true},{\"key\":\"jobService.datumDataSource.frequency\",\"value\":\"50.0\",\"default\":true},{\"key\":\"jobService.datumDataSource.resistance\",\"value\":\"10.0\",\"default\":true},{\"key\":\"jobService.datumDataSource.inductance\",\"value\":\"10.0\",\"default\":true},{\"key\":\"jobService.datumDataSource.randomness\",\"value\":\"true\"},{\"key\":\"jobService.datumDataSource.voltdev\",\"value\":\"3\"},{\"key\":\"jobService.datumDataSource.freqdev\",\"value\":\"0.1\"},{\"key\":\"jobService.datumDataSource.resistanceDeviation\",\"value\":\"5.0\"},{\"key\":\"jobService.datumDataSource.inductanceDeviation\",\"value\":\"2.0\"}]"
	}
	```

=== "Result JSON"

	Parsing the `result` value as JSON you can see the result structure more clearly:

	```json
	[
		{"key":"schedule","value":"6000"},
		{"key":"jobService.datumDataSource.uid","value":"","default":true},
		{"key":"jobService.datumDataSource.groupUid","value":"","default":true},
		{"key":"jobService.datumDataSource.publishDeviceInfoMetadata","value":true,"default":true},
		{"key":"jobService.datumDataSource.sourceId","value":"/power/1"},
		{"key":"jobService.datumDataSource.voltage","value":"230.0","default":true},
		{"key":"jobService.datumDataSource.frequency","value":"50.0","default":true},
		{"key":"jobService.datumDataSource.resistance","value":"10.0","default":true},
		{"key":"jobService.datumDataSource.inductance","value":"10.0","default":true},
		{"key":"jobService.datumDataSource.randomness","value":"true"},
		{"key":"jobService.datumDataSource.voltdev","value":"3"},
		{"key":"jobService.datumDataSource.freqdev","value":"0.1"},
		{"key":"jobService.datumDataSource.resistanceDeviation","value":"5.0"},
		{"key":"jobService.datumDataSource.inductanceDeviation","value":"2.0"}
	]
	```

!!! note

	If the `spec` instruction parameter is `true`, then [specification objects](#specification-mode)
	will be returned instead of setting objects.

#### Specification Mode

If the `spec` instruction parameter has the value `true` then the structure of the [List
settings](#list-settings) and [List factory instance settings](#list-factory-instance-settings)
results change into _specification objects_ instead of setting objects. Each specification object
has a minimum of a `type` property that represents the type of setting. Different setting types
provide other properties.

Here is an example specification result decoded to JSON:

```json
[
    {
        "key": "string",
        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier",
        "defaultValue": "simple"
    },
    {
        "key": "password",
        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier",
        "secureTextEntry": true
    },
    {
        "key": "integer",
        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier",
        "defaultValue": "42"
    },
    {
        "key": "toggle",
        "type": "net.solarnetwork.settings.ToggleSettingSpecifier",
        "defaultValue": false,
        "falseValue": false,
        "trueValue": true
    },
    {
        "key": "slide",
        "type": "net.solarnetwork.settings.SliderSettingSpecifier",
        "defaultValue": 5.0,
        "minimumValue": 0.0,
        "maximumValue": 10.0,
        "step": 0.5
    },
    {
        "key": "radio",
        "type": "net.solarnetwork.settings.RadioGroupSettingSpecifier",
        "defaultValue": "One",
        "valueTitles": {
            "One": "One",
            "Two": "Two",
            "Three": "Three"
        }
    },
    {
        "key": "menu",
        "type": "net.solarnetwork.settings.MultiValueSettingSpecifier",
        "defaultValue": "Option 1",
        "valueTitles": {
            "Option 1": "Option 1",
            "Option 2": "Option 2",
            "Option 3": "Option 3"
        }
    },
    {
        "key": "locationKey",
        "type": "net.solarnetwork.node.settings.LocationLookupSettingSpecifier",
        "defaultValue": 11536821,
        "locationTypeKey": "price"
    },
    {
        "key": "weatherLocationKey",
        "type": "net.solarnetwork.node.settings.LocationLookupSettingSpecifier",
        "defaultValue": 11536821,
        "locationTypeKey": "weather"
    },
    {
        "key": "textAreaDirect",
        "type": "net.solarnetwork.settings.TextAreaSettingSpecifier",
        "direct": true
    },
    {
        "key": "textArea",
        "type": "net.solarnetwork.settings.TextAreaSettingSpecifier"
    },
    {
        "key": "file",
        "type": "net.solarnetwork.node.settings.FileSettingSpecifier",
        "acceptableFileTypeSpecifiers": [
            ".txt",
            "text/*"
        ]
    },
    {
        "key": "textFiles",
        "type": "net.solarnetwork.node.settings.FileSettingSpecifier",
        "acceptableFileTypeSpecifiers": [
            ".txt",
            "text/*"
        ],
        "multiple": true
    },
    {
        "type": "net.solarnetwork.node.settings.SetupResourceSettingSpecifier",
        "setupResourceProperties": {"foo": "bar"}
    },
    {
        "type": "net.solarnetwork.settings.GroupSettingSpecifier",
        "dynamic": true,
        "groupSettings": [
            {
                "key": "listString[0]",
                "type": "net.solarnetwork.settings.TextFieldSettingSpecifier"
            }
        ]
    },
    {
        "type": "net.solarnetwork.settings.GroupSettingSpecifier",
        "dynamic": true,
        "groupSettings": [
            {
                "type": "net.solarnetwork.settings.GroupSettingSpecifier",
                "dynamic": false,
                "groupSettings": [
                    {
                        "key": "listComplex[0].firstName",
                        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier"
                    },
                    {
                        "key": "listComplex[0].lastName",
                        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier"
                    },
                    {
                        "key": "listComplex[0].genderValue",
                        "type": "net.solarnetwork.settings.MultiValueSettingSpecifier",
                        "defaultValue": "Other",
                        "valueTitles": {
                            "Male": "Male",
                            "Female": "Female",
                            "Other": "Other"
                        }
                    },
                    {
                        "key": "listComplex[0].phone",
                        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier"
                    },
                    {
                        "key": "listComplex[0].age",
                        "type": "net.solarnetwork.settings.TextFieldSettingSpecifier"
                    },
                    {
                        "key": "listComplex[0].enabled",
                        "type": "net.solarnetwork.settings.ToggleSettingSpecifier",
                        "defaultValue": true,
                        "falseValue": false,
                        "trueValue": true
                    }
                ]
            }
        ]
    }
]
```

##### Group Specification

The `net.solarnetwork.settings.GroupSettingSpecifier` specification `type` represents a group of
other specifications. This object will include a `dynamic` boolean property, that, when `true`,
represents a dynamic-sized list of settings. The `groupSettings` property will be an array of
other specification objects.

##### Other Specifications

Most other specification objects provide a `key` property that represents the unique identifier
for the setting.


[wildcards]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#wildcard-patterns
