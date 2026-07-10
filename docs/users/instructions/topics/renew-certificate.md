---
title: RenewCertificate
---
# `RenewCertificate` topic

This instruction asks a node to install a renewed X.509 client certificate.

=== "RenewCertificate example"

	```json
	{
		"nodeId": 123,
		"topic": "RenewCertificate",
		"parameters": [
			{
				"name": "Certificate",
				"value": "-----BEGIN CERTIFICATE CHAIN-----\nMIILGgYJKoZIh..."
			},
			{
				"name": "Certificate",
				"value": "...Qe1lZeV2MQA=\n-----END CERTIFICATE CHAIN-----"
			}
		]
	}
	```

Because instruction parameter values have a limited maximum length, the instruction will
split the certificate data into more than one `Certificate` parameter. SolarNode will
join all `Certificate` parameters into one value to decode the certificate chain.

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `Certificate` | The PEM-encoded PKCS7 renewed certificate chain to install. |

## Result

A `Completed` state indicates the certificate was installed successfully.
