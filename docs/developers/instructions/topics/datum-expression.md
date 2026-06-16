---
title: DatumExpression
---
# `DatumExpression` topic

This instructions asks a node to evaluate a [datum expression](../../../users/expressions.md) and return the result.

=== "DatumExpression example"

	```json
	{
		"nodeId": 123,
		"topic": "DatumExpression",
		"parameters": [
			{
				"name": "expression",
				"value": "sum(latestMatching('/power/*').![watts * powerFactor])"
			}
		]
	}
	```

=== "DatumExpression example (compact)"

	```json
	{
		"nodeId": 123,
		"topic": "DatumExpression",
		"params": {
			"expression": "sum(latestMatching('/power/*').![watts * powerFactor])"
		}
	}
	```

## Parameters

The following instruction parameters are available:

| Parameter | Description |
|:----------|:------|
| `executionDate` | A future execution date. See [deferred instructions](../index.md#deferred-instructions) for more details. |
| `expression` | The `id` of the instruction to cancel. |
| `lang` | The ID of the expression language to use. See table below for possible values. If not provided then SpEL will be assumed. |

### Supported expression languages

The following `lang` parameter values are supported:

| Language | ID |
|:---------|:---|
| [SpEL][spel] | `net.solarnetwork.common.expr.spel.SpelExpressionService` |


## Result

Once executed, a `result` result parameter will be provided with the expression result. For example:

```json title="DatumExpression example result"
{
	"nodeId": 123,
	"topic": "DatumExpression",
	"statusDate": "2024-04-03 17:27:41.601474Z",
    "state": "Completed",
	"parameters": [
		{
			"name": "expression",
			"value": "sum(latestMatching('/power/*').![watts * powerFactor])"
		}
	],
    "resultParameters": {
    	"result": 12359.99964279
    }
}
```


[spel]: https://github.com/SolarNetwork/solarnetwork/wiki/Spring-Expression-Language-Syntax
