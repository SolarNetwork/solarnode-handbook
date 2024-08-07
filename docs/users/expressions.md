# Expressions

Many SolarNode components support a general "expressions" framework that can be used to calculate
values using a scripting language. SolarNode comes with the [Spel][spel] scripting language by
default, so this guide describes that language.

A common use case for expressions is to derive [datum][datum] property values out of the raw
property values captured from a device. In the SolarNode [Setup App](setup-app/index.md) a typical
datum data source component might present a configurable list of expression settings like this:

<figure markdown>
  ![Example expression settings](../images/users/expression-example-settings%402x.png){width=706 loading=lazy}
</figure>

In this example, each time the data source captures a datum from the device it is communicating with
it will add a new `watts` property by multiplying the captured `amps` and `volts` property values.
In essence the expression is like this code:

```
watts = amps × volts
```

## Datum Expressions

Many SolarNode expressions are evaluated in the context of a [datum][datum], typically one captured
from a device SolarNode is collecting data from. In this context, the expression supports accessing
datum properties directly as expression variables, and some helpful functions are provided.

### Datum property variables

All datum properties with _simple_ names can be referred to directly as variables. Here _simple_
just means a name that is also a [legal variable name][expression-variable-names]. The [property
classifications][datum-prop-class] do not matter in this context: the expression will look for
properties in all classifications.

For example, given a datum like this:

```json title="Example datum representation in JSON"
{
  "i": {
    "watts"     : 123
  },
  "a": {
    "wattHours" : 987654321
  },
  "s": {
    "mode"      : "auto"
  }
}
```

The expression can use the variables `watts`, `wattHours`, and `mode`.

### Other variables

A datum expression will also provide the following variables:

| Property | Type | Description |
|:---------|:-----|:------------|
| `datum` | `Datum` | A [`Datum`][datum-class] object, in case you need direct access to the functions provided there. |
| `locId` | `Number` | For location Datum, the location ID of the datum. |
| `meta`  | `DatumMetadataOperations` | Get [datum metadata](#datum-metadata) for the current source ID. |
| `parameters` | `Map<String,Object>` | Simple map-based access to all parameters passed to the expression. The available parameters depend on the context of the expression evaluation, but often include things like [placeholder][sn-placeholders] values or parameters generated by previously evaluated expressions. These values are also available directly as variables, this is rarely needed but can be helpful for accessing dynamically-calculated property names or properties with names that are not legal variable names. |
| `props` | `Map<String,Object>` | Simple map based access to all properties in `datum`. As datum properties are also available directly as variables, this is rarely needed but can be helpful for accessing dynamically-calculated property names or properties with names that are not legal variable names. |
| `sourceId` | `String` | The source ID of the current datum. |

### Functions

Some functions are provided to help with datum-related expressions.

#### Bit functions

The following functions help with bitwise integer manipulation operations:

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `and(n1,n2)` | `Number`, `Number` | `Number` | Bitwise and, i.e. `(n1 & n2)`  |
| `andNot(n1,n2)` | `Number`, `Number` | `Number` | Bitwise and-not, i.e. `(n1 & ~n2)`  |
| `narrow(n,s)` | `Number`, `Number` | `Number` | Return `n` as a reduced-size but equivalent number of a minimum power-of-two byte size `s` |
| `narrow8(n)` | `Number` | `Number` | Return `n` as a reduced-size but equivalent number narrowed to a minimum of 8-bits |
| `narrow16(n)` | `Number` | `Number` | Return `n` as a reduced-size but equivalent number narrowed to a minimum of 16-bits |
| `narrow32(n)` | `Number` | `Number` | Return `n` as a reduced-size but equivalent number narrowed to a minimum of 32-bits |
| `narrow64(n)` | `Number` | `Number` | Return `n` as a reduced-size but equivalent number narrowed to a minimum of 64-bits |
| `not(n)` | `Number` | `Number` | Bitwise not, i.e. `(~n)`  |
| `or(n1,n2)` | `Number`, `Number` | `Number` | Bitwise or, i.e. `(n1 | n2)`  |
| `shiftLeft(n,c)` | `Number`, `Number` | `Number` | Bitwise shift left, i.e. `(n << c)`  |
| `shiftRight(n,c)` | `Number`, `Number` | `Number` | Bitwise shift left, i.e. `(n >> c)`  |
| `testBit(n,i)` | `Number`, `Number` | `boolean` | Test if bit `i` is set in integer `n`, i.e. `((n & (1 << i)) != 0)`  |
| `xor(n1,n2)` | `Number`, `Number` | `Number` | Bitwise xor, i.e. `(n1 ^ n2)`  |

!!! tip

	 All number arguments will be converted to `BigInteger` values for the bitwise operations, and
	 `BigInteger` values are returned.

#### Datum stream functions

The following functions deal with datum streams. The `latest()` and `offset()` functions give you
access to recently-captured datum from any SolarNode source, so you can refer to any datum stream
being generated in SolarNode. They return another datum expression root object, which means you have
access to all the variables and functions documented on this page with them as well.

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `hasLatest(source)`          | `String` | `boolean` | Returns `true` if a datum with source ID `source` is available via the `latest(source)` function. |
| `hasLatestMatching(pattern)` | `String` | `Collection<DatumExpressionRoot>` | Returns `true` if `latestMatching(pattern)` returns a non-empty collection. |
| `hasLatestOtherMatching(pattern)` | `String` | `Collection<DatumExpressionRoot>` | Returns `true` if `latestOthersMatching(pattern)` returns a non-empty collection. |
| `hasMeta()`                  |  | `boolean` | Returns `true` if metadata for the current source ID is available. |
| `hasMeta(source)`            | `String` | `boolean` | Returns `true` if `datumMeta(source)` would return a non-null value. |
| `hasOffset(offset)`          | `int` | `boolean` | Returns `true` if a datum is available via the `offset(offset)` function. |
| `hasOffset(source,offset)`   | `String`, `int` | `boolean` | Returns `true` if a datum with source ID `source` is available via the `offset(source,int)` function. |
| `latest(source)`             | `String` | `DatumExpressionRoot` | Provides access to the latest available datum matching the given source ID, or `null` if not available. This is a shortcut for calling `offset(source,0)`. |
| `latestMatching(pattern)`    | `String` | `Collection<DatumExpressionRoot>` | Return a collection of the latest available datum matching a given source ID [wildcard pattern][wildcards].  |
| `latestOthersMatching(pattern)` | `String` | `Collection<DatumExpressionRoot>` | Return a collection of the latest available datum matching a given source ID [wildcard pattern][wildcards], excluding the current datum if its source ID happens to match the pattern.  |
| `locMeta(locId,source)`      | `long`, `String` | `DatumMetadataOperations` | Get [datum metadata](#datum-metadata) for a specific location datum stream. |
| `meta(source)`               | `String` | `DatumMetadataOperations` | Get [datum metadata](#datum-metadata) for a specific source ID. |
| `metaMatching(pattern)`      | `String` | `Collection<DatumMetadataOperations>` | Find [datum metadata](#datum-metadata) for sources matching a given source ID [wildcard pattern][wildcards]. |
| `offset(offset)`             | `int` | `DatumExpressionRoot` | Provides access to a datum from the same stream as the current datum, offset by `offset` in time, or `null` if not available. Offset `1` means the datum just before this datum, and so on. |
| `offset(source,offset)`      | `String`, `int` | `DatumExpressionRoot` | Provides access to an offset from the latest available datum matching the given source ID, or `null` if not available. Offset `0` represents the "latest" datum, `1` the one before that, and so on. SolarNode only maintains a limited history for each source, do do not rely on more than a few datum to be available via this method. This history is also cleared when SolarNode restarts. |
| `selfAndLatestMatching(pattern)` | `String` | `Collection<DatumExpressionRoot>` | Return a collection of the latest available datum matching a given source ID [wildcard pattern][wildcards], including the current datum. The current datum will always be the first datum returned. |


#### Math functions

Expressions support basic [math operators][spel-math] like `+` for addition and `*` for
multiplication. The following functions help with other math operations:

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `avg(collection)` | `Collection<Number>` | `Number` | Calculate the average (mean) of a collection of numbers. Useful when combined with the `group(pattern)` function. |
| `ceil(n)` | `Number` | `Number` | Round a number larger, to the nearest integer. |
| `ceil(n,significance)` | `Number`, `Number` | `Number` | Round a number larger, to the nearest integer multiple of `significance`. |
| `down(n)` | `Number` | `Number` | Round numbers towards zero, to the nearest integer. |
| `down(n,significance)` | `Number`, `Number` | `Number` | Round numbers towards zero, to the nearest integer multiple of `significance`. |
| `exp(n)` | `Number` | `Number`| Returns Euler's number _e_ raised to the power of `n`. |
| `floor(n)` | `Number` | `Number` | Round a number smaller, to the nearest integer. |
| `floor(n,significance)` | `Number`, `Number` | `Number` | Round a number smaller, to the nearest integer multiple of `significance`. |
| `interp(x, x1, x2, y1, y2)` | `Number`, `Number`, `Number`, `Number`, `Number` | `Number` | Linearly interpolate `x` over the range `x1` to `x2`, mapped to output range from `y1` to `y2`. |
| `interp(x, x1, x2, y1, y2, scale)` | `Number`, `Number`, `Number`, `Number`, `Number`, `Number` | `Number` | Linearly interpolate `x` over the range `x1` to `x2`, mapped to output range from `y1` to `y2` rounding to at most `scale` decimal places. |
| `max(collection)` | `Collection<Number>` | `Number` | Return the largest value from a set of numbers. |
| `max(n1,n2)` | `Number`, `Number` | `Number` | Return the larger of two numbers. |
| `min(collection)` | `Collection<Number>` | `Number` | Return the smallest value from a set of numbers. |
| `min(n1,n2)` | `Number`, `Number` | `Number` | Return the smaler of two numbers. |
| `mround(n,significance)` | `Number`, `Number` | `Number` | Round a number to the nearest integer multiple of `significance`. |
| `round(n)` | `Number` | `Number` | Round a number to the nearest integer. |
| `round(n,digits)` | `Number`, `Number` | `Number` | Round a number to the nearest number with `digits` decimal digits. |
| `roundDown(n,digits)` | `Number`, `Number` | `Number` | Round a number towards zero to the nearest number with `digits` decimal digits. |
| `roundUp(n,digits)` | `Number`, `Number` | `Number` | Round a number away from zero to the nearest number with `digits` decimal digits. |
| `sum(collection)` | `Collection<Number>` | `Number` | Calculate the sum of a collection of numbers. Useful when combined with the `group(pattern)` function. |
| `up(n)` | `Number` | `Number` | Round numbers away from zero, to the nearest integer. |
| `up(n,significance)` | `Number`, `Number` | `Number` | Round numbers away from zero, to the nearest integer multiple of `significance`. |


#### Node metadata functions

All the [Datum Metadata](#datum-metadata) functions like `metadataAtPath(path)` can be invoked
directly, operating on the node's own metadata instead of a datum stream's metadata.

#### Operational functions

The following functions deal with general SolarNode operations:

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `isOpMode(mode)` | `String` | `boolean` | Returns `true` if the `mode` operational mode is active. |


#### Property functions

The following functions help with expression properties (variables):

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `has(name)` | `String` | `boolean` | Returns `true` if a property named `name` is defined. Can be used to prevent expression errors on datum property variables that are missing. |
| `group(pattern)` | `String` | `Collection<Number>` | Creates a collection out of numbered properties whose `name` matches the given regular expression `pattern`. |

## Expression examples

Let's assume a captured datum like this, expressed as JSON:

```json
{
  "i" : {
    "amps" 		: 4.2,
    "volts" 	: 240.0
  },
  "a" : {
    "reading" 	: 38009138
  },
  "s" : {
    "state" 	: "Ok"
  }
}
```

Then here are some example [Spel][spel] expressions and the results they would produce:

| Expression | Result | Comment |
|:-----------|:-------|:--------|
| `state` | `Ok` | Returns the `state` status property directly, which is `Ok`. |
| `datum.s['state']` | `Ok` | Returns the `state` status property explicitly. |
| `props['state']` | `Ok` | Same result as `datum.s['state']` but using the short-cut `props` accessor. |
| `amps * volts` | `1008.0` | Returns the result of multiplying the `amps` and `volts` properties together: `4.2 × 240.0 = 1008.0`. |

### Datum stream history

Building on the previous example datum, let's assume an **earlier** datum for the same source ID had
been collected with these properties (the classifications have been omitted for brevity):

```json
{
  "amps"    : 3.1,
  "volts"   : 241.0,
  "reading" : 38009130,
  "state"   : "Ok"
}
```

Then here are some example expressions and the results they would produce given the original datum
example:

| Expression | Result | Comment |
|:-----------|:-------|:--------|
| `hasOffset(1)` | `true` | Returns `true` because of the earlier datum that is available. |
| `hasOffset(2)` | `false` | Returns `false` because only one earlier datum is available. |
| `amps - offset(1).amps` | `1.1` | Computes the difference between the current and previous `amps` properties, which is `4.2 - 3.1 = 1.1`. |

### Other datum stream history

Other datum stream histories collected by SolarNode can also be accessed via the
`offset(source,offset)` function. Let's assume SolarNode is collecting a datum stream for the source
ID `solar`, and had amassed the following history, in newest-to-oldest order:

```json
[
  {"amps" : 6.0, "volts" : 240.0 },
  {"amps" : 5.9, "volts" : 239.9 }
]
```

Then here are some example expressions and the results they would produce given the original datum
example:

| Expression | Result | Comment |
|:-----------|:-------|:--------|
| `hasLatest('solar')` | `true` | Returns `true` because of a datum for source `solar` is available. |
| `hasOffset('solar',2)` | `false` | Returns `false` because only one earlier datum from the latest with source `solar` is available. |
| `(amps * volts) - (latest('solar').amps * latest('solar').volts)` | `432.0` | Computes the difference in energy between the latest `solar` datum and the current datum, which is `(6.0 × 240.0) - (4.2 × 240.0) = 432.0`. |

If we add another datum stream for the source ID `solar1` like this:

```json
[
  {"amps" : 1.0, "volts" : 240.0 }
]
```

If we also add another datum stream for the source ID `solar2` like this:

```json
[
  {"amps" : 3.0, "volts" : 240.0 }
]
```

Then here are some example expressions and the results they would produce given the previous datum
examples:

| Expression | Result | Comment |
|:-----------|:-------|:--------|
| `sum(latestMatching('solar*').?[amps>1].![amps * volts])` | `2160` | Returns the sum power of the latest `solar` and `solar2` datum. The `solar1` power is omitted because its `amps` property is not greater than `1`, so we end up with `(6 * 240) + (3 * 240) = 2160`. |


## Datum metadata

Some functions return [`DatumMetadataOperations`][DatumMetadataOperations] objects. These objects
provide [metadata][sn-metadata] for things like a specific source ID on SolarNode.

### Datum metadata properties

The properties available on datum metadata objects are:

| Property | Type | Description |
|:---------|:-----|:------------|
| `empty`            | `boolean` | Is `true` if the metadata does not contain any values. |
| `info`             | `Map<String,Object>` | Simple map based access to the general metadata (e.g. the keys of the [`m` metadata][sn-metadata] map). |
| `infoKeys`         | `Set<String>` | The set of general metadata keys available (e.g. the keys of the [`m` metadata][sn-metadata] map). |
| `propertyInfoKeys` | `Set<String>` | The set of property metadata keys available (e.g. the keys of the [`pm` metadata][sn-metadata] map). |
| `tags`             | `Set<String>` | A set of tags associated with the metadata. |

### Datum metadata general info functions

The following functions available on datum metadata objects support access to the general
metadata (e.g. the [`m` metadata][sn-metadata] map):

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `getInfo(key)` | `String` | `Object` | Get the general metadata value for a specific key. |
| `getInfoNumber(key)` | `String` | `Number` | Get a general metadata value for a specific key as a `Number`. Other more specific number value functions are also available such as `getInfoInteger(key)` or `getInfoBigDecimal(key)`. |
| `getInfoString(key)` | `String` | `String` | Get a general metadata value for a specific key as a `String`. |
| `hasInfo(key)` | `String` | `boolean` | Returns `true` if a non-null general metadata value exists for the given key. |

### Datum metadata property info functions

The following functions available on datum metadata objects support access to the property
metadata (e.g. the [`pm` metadata][sn-metadata] map):

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `getPropertyInfo(prop)` | `String` | `Map<String,Object>` | Get the property metadata for a specific property. |
| `getInfoNumber(prop,key)` | `String`, `String` | `Number` | Get a property metadata value for a specific property and key as a `Number`. Other more specific number value functions are also available such as `getInfoInteger(prop,key)` or `getInfoBigDecimal(prop,key)`. |
| `getInfoString(prop,key)` | `String`, `String` | `String` | Get a property metadata value for a specific property and key as a `String`. |
| `hasInfo(prop,key)` | `String`, `String` | `String` | Returns `true` if a non-null property metadata value exists for the given property and key. |

### Datum metadata global functions

The following functions available on datum metadata objects support access to both general and property metadata:

| Function | Arguments | Result | Description |
|:---------|:----------|:-------|:------------|
| `differsFrom(metadata)` | `DatumMetadataOperations` | `boolean` | Returns `true` if the given metadata has any different values than the receiver. |
| `hasTag(tag)` | `String` | `boolean` | Returns `true` if the given tag is available. |
| `metadataAtPath(path)` | `String` | `Object` | Get the metadata value at a [metadata key path][sn-metadata-path]. |
| `hasMetadataAtPath(path)` | `String` | `boolean` | Returns `true` if `metadataAtPath(path)` would return a non-null value. |


[datum]: datum.md
[datum-class]: https://github.com/SolarNetwork/solarnetwork-common/blob/develop/net.solarnetwork.common/src/net/solarnetwork/domain/datum/Datum.java
[datum-prop-class]: datum.md#datum-property-classifications
[DatumMetadataOperations]: https://github.com/SolarNetwork/solarnetwork-common/blob/develop/net.solarnetwork.common/src/net/solarnetwork/domain/datum/DatumMetadataOperations.java
[expression-root]: https://github.com/SolarNetwork/solarnetwork/wiki/Spring-Expression-Language#root-object
[expression-variable-names]: https://github.com/SolarNetwork/solarnetwork/wiki/Spring-Expression-Language#variable-names
[sn-metadata]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#metadata
[sn-metadata-path]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#metadata-filter-key-paths
[sn-placeholders]: placeholders.md
[spel]: https://github.com/SolarNetwork/solarnetwork/wiki/Spring-Expression-Language
[spel-math]: https://github.com/SolarNetwork/solarnetwork/wiki/Spring-Expression-Language-Syntax#mathematical-operators
[wildcards]: https://github.com/SolarNetwork/solarnetwork/wiki/SolarNet-API-global-objects#wildcard-patterns
