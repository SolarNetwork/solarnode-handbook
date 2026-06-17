---
title: SystemConfigure
---
# `SystemConfigure` topic

This instruction requests the node to manage system configuration. The available system configuration
services that can be configured is dependent on the plugins implementing support for this instruction.
Some standard services are documented here.

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `service` | The ID of the service to get the configuration of. |

Each service implementation can support additional parameters. See [below](#standard-system-configure-services) for more details.

## Result

A `Completed` instruction indicates the configuration has been applied.

## Standard system configure services

The following `service` values represent some standard services supported in SolarNode:

| Service | Description |
|:--------|:------------|
| [`/setup/datum/latest`](#datum-latest-service) | Will return a the most recently captured datum. |
| [`/setup/identity`](#identity-service) | Will return information about the node's identity. |
| [`/setup/network/wifi`](#network-wifi-service) | Manage WiFi network settings. |

### Datum latest service

The `/setup/datum/latest` service is a core service provided by SolarNode, and can return a list of
the most recently captured datum per source ID.

=== "Datum latest example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"parameters": [
			{"name": "service", "value": "/setup/datum/latest"},
			{"name": "arg", "value": "/inv/1"}
		]
	}
	```

=== "Datum latest example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"params": {
			"service": "/setup/datum/latest",
			"arg": "/inv/1,/inv/2"
		}
	}
	```

=== "JSON source ID filter"

	The `arg` filter can also be specified as a JSON array instead of a comma-delimited list:

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"params": {
			"service": "/setup/datum/latest",
			"arg": "[\"/inv/1\",\"/inv/2\"]"
		}
	}
	```

#### Datum latest parameters

The additional parameters supported by this instruction are:

| Parameter | Description |
|:----------|:------------|
| `arg`  | An optional source ID filter to restrict the results to, as either a JSON array or comma-delimited list. |

#### Datum latest result

The `result` result parameter will be an array of datum objects.

=== "Datum latest result"

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 21:39:09.154986Z",
			"nodeId": 123,
			"topic": "SystemConfigure",
			"instructionDate": "2024-06-11 21:39:09.154867Z",
			"statusDate": "2024-06-11 21:39:09.649036Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "/setup/datum/latest"
				},
				{
					"name": "arg",
					"value": "/inv/1"
				}
			],
			"resultParameters": {
				"result": [
					{
						"created": "2024-06-11 21:39:00Z",
						"sourceId": "/inv/1",
						"i": {
							"frequency": 60,
							"watts": 133000,
							"reactivePower": 7,
							"voltage_ab": 473,
							"voltage_bc": 476,
							"voltage_ca": 473
						},
						"a": {
							"wattHoursReverse": 0,
							"wattHours": 0
						},
						"s": {
							"offline": 0
						}
					}
				]
			}
		}
	}
	```


### Identity service

The `/setup/identity` service is a core service provided by SolarNode, and can return identifying
information about the node.

=== "Identity example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"parameters": [
			{"name": "service", "value": "/setup/identity"}
		]
	}
	```

=== "Identity example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"params": {
			"service": "/setup/identity"
		}
	}
	```

#### Identity parameters

No additional parameters are supported by this instruction.

#### Identity result

The `result` result parameter will be an identity object with the following properties:

| Property | Type | Description |
|:---------|:-----|:------------|
| `nodeId` | number | The node ID. |
| `solarInBaseUrl` | string | The SolarIn base URL (the service SolarNode is connected to). |
| `nodeCertificateDn` | string | The distinguished name of the node's issued certificate. |
| `nodeCertificateIssuerDn` | string | The distinguished name of the node's certificate issuer. |
| `nodeCertificateSerialNumber` | string | The node's certificate serial number, as a hex-encoded (base 16) string. |
| `nodeCertificateValidToDate` | string | The node certificate's expiration date, as an ISO 8601 timestamp string. |
| `nodeCertificateValidFromDate` | string | The node certificate's starting date, as an ISO 8601 timestamp string. |

=== "Identity result"

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 21:39:09.154986Z",
			"nodeId": 123,
			"topic": "SystemConfigure",
			"instructionDate": "2024-06-11 21:39:09.154867Z",
			"statusDate": "2024-06-11 21:39:09.649036Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "/setup/identity"
				}
			],
			"resultParameters": {
				"result": {
					"nodeId": 123,
					"solarInBaseUrl": "https://in.solarnetwork.net/solarin",
					"nodeCertificateIssuerDn": "CN=SolarNetwork Root CA,OU=SolarNetwork Certification Authority,O=SolarNetwork",
					"nodeCertificateSerialNumber": "0xf1a",
					"nodeCertificateValidToDate": "2032-08-07T03:31:01Z",
					"nodeCertificateDn": "UID=123,O=SolarNetwork",
					"nodeCertificateValidFromDate": "2022-08-09T03:31:01Z"
				}
			}
		}
	}
	```

### Network WiFi service

The `/setup/network/wifi` service can be used to view the status of the node's WiFi network or
manage the node's WiFi network settings (such as the network name and password). It is provided by
the WiFi Setup plugin, which is included in the [solarnode-app-setup-wifi][solarnode-app-setup-wifi]
package in SolarNodeOS.

=== "View WiFi status example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"parameters": [
			{"name": "service", "value": "/setup/network/wifi"}
		]
	}
	```

=== "Update WiFi settings example"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"parameters": [
			{"name": "service", "value": "/setup/network/wifi"},
			{"name": "ssid", "value": "My WiFi Network"},
			{"name": "password", "value": "super-secret-password"},
		]
	}
	```

=== "Update WiFi settings example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "SystemConfigure",
		"params": {
			"service": "/setup/network/wifi",
			"ssid": "My WiFi Network",
			"password": "super-secret-password"
		}
	}
	```

#### Network WiFi parameters

The additional parameters supported by this instruction are:

| Parameter | Description |
|:----------|:------------|
| `country`  | The WiFi country code to use. |
| `password` | The WiFi password to use. |
| `ssid`  | The WiFi network name to connect to. |

#### Network WiFi result

With no additional instruction parameters are given, the `result` result parameter will be a status
object with the following properties:

| Property | Type | Description |
|:---------|:-----|:------------|
| `active` | boolean | `true` if the WiFi connection is active. |
| `addresses` | string[] | A list of IP addresses assigned to the WiFi connection. |

When connection setting parameters are given, the `result` result parameter will be a list of
setting values:

 1. The country
 2. The SSID name

=== "Network WiFi result (status)"

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 21:39:09.154986Z",
			"nodeId": 123,
			"topic": "SystemConfigure",
			"instructionDate": "2024-06-11 21:39:09.154867Z",
			"statusDate": "2024-06-11 21:39:09.649036Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "/setup/network/wifi"
				}
			],
			"resultParameters": {
				"result": {
					"active": true,
					"addresses": [
						"192.168.1.1",
						"2406:e006:3093:b301:65b1:4726:2af:d721"
					]
				}
			}
		}
	}
	```

=== "Network WiFi result (change settings)"

	```json
	{
		"success": true,
		"data": {
			"id": 123123123,
			"created": "2024-06-11 21:39:09.154986Z",
			"nodeId": 123,
			"topic": "SystemConfigure",
			"instructionDate": "2024-06-11 21:39:09.154867Z",
			"statusDate": "2024-06-11 21:39:09.649036Z",
			"state": "Completed",
			"parameters": [
				{
					"name": "service",
					"value": "/setup/network/wifi"
				},
				{
					"name": "ssid",
					"value": "My WiFi Network"
				}
			],
			"resultParameters": {
				"result": [
					"NZ",
					"My WiFi Network"
				]
			}
		}
	}
	```

[solarnode-app-setup-wifi]: https://github.com/SolarNetwork/solarnode-os-packages/tree/develop/solarnode-app-setup-wifi/debian
