# Tariff Schedules

Tariff schedules are a convenient way to represent _variable rates_ that vary based on _time
constraints_. Some common use cases for tariff schedules include:

 * **Time-of-use based energy pricing**: a variable-rate pricing scheme, such as time-of-day or day-of-week (or both)
 * **Modelled PV energy production**: the expected theoretical PV energy output of a system can be used to track system performance
 * **Seasonal operating modes**: season-based parameters can be encoded into a tariff schedule and used by [expressions](./expressions.md)

Tariff schedules are commonly stored in [metadata](./metadata.md), so components like the [Fixed Data](./datum-sources/fixed.md) datum source
or the [Tariff Filter](./datum-filters/tariff.md) can make use of them.

## Tariff schedule format

A tariff schedule uses a simple CSV-based format that can be easily exported from a spreadsheet.
Each row represents a rule that includes:

 * a set of time constraints that must be satisfied for the rule to be applied
 * a list of tariff rates associated with the time constraints

The schedule consists of 4 time constraint columns followed by one or more tariff rate columns. Each
constraint is represented as a range, in the form `start - end`. Whitespace is allowed around the
`-` character. If the `start` and `end` are the same, the range may be shortened to just `start`. A
range can be left empty to represent **all values**. The time constraint columns are:

| Column | Constraint | Description |
|:-------|:-----------|:------------|
| 1      | Month range | An inclusive month range. Months can be specified as numbers (1-12) or abbreviations (Jan-Dec) or full names (January - December). When using text names case does not matter and they will be typically be parsed using a **Lanauage** setting configured on the component that is accessing the schedule. |
| 2      | Day range | An inclusive day-of-month range. Days are specified as numbers (1-31). |
| 3      | Weekday range | An inclusive day-of-week range. Weekdays can be specified as numbers (1-7) with Monday being `1` and Sunday being `7`, or abbreviations (Mon-Sun) or full names (Monday - Sunday). When using text names case does not matter and they will be parsed using a **Lanauage** setting configured on the component that is accessing the schedule.. |
| 4      | Time range | An **inclusive - exclusive** time-of-day range. The time can be specified as whole hour numbers (0-24) or `HH:MM` style (`00:00` - `24:00`). |

Starting on column 5 of the tariff schedule are arbitrary rate values to add to datum when the
corresponding constraints are satisfied. The name of the datum property is derived from the **header
row** of the column, adapted according to the following rules:

 1. change to lower case
 2. replacing any runs of non-alphanumeric or underscore with a single underscore
 3. removing any leading/trailing underscores

Here are some examples of the header name to the equivalent property name:

| Rate Header Name         | Datum Property Name |
|:-------------------------|:--------------------|
| TOU                      | `tou`               |
| Foo Bar                  | `foo_bar`           |
| This Isn't A Great Name! | `this_isn_t_a_great_name` |

## Example schedule

Here's an example schedule with 4 rules and a single **TOU** rate (the `*` stands for **all values**):

| Rule  | Month   | Day | Weekday | Time |   TOU |
|:------|:--------|:----|:--------|:-----|------:|
| **1** | Jan-Dec | *   | Mon-Fri | 0-8  | 10.48 |
| **2** | Jan-Dec | *   | Mon-Fri | 8-24 | 11.00 |
| **3** | Jan-Dec | *   | Sat-Sun | 0-8  |  9.19 |
| **4** | Jan-Dec | *   | Sat-Sun | 8-24 | 11.21 |


In CSV format the schedule would look like this:

```csv
Month,Day,Weekday,Time,TOU
Jan-Dec,,Mon-Fri,0-8,10.48
Jan-Dec,,Mon-Fri,8-24,11.00
Jan-Dec,,Sat-Sun,0-8,9.19
Jan-Dec,,Sat-Sun,8-24,11.21
```

!!! tip "Include a header row"

	A header row is **required** because the tariff rate names are defined there.
	The first 4 column names are ignored.

## Metadata encoding

When encoding into SolarNetwork metadata JSON, the schedule can be encoded either as a JSON string
or an array-of-arrays structure. The previous example schedule would look like this when saved
at the `/pm/tariffs/schedule` path as a string:

```json
{
  "pm": {
    "tariffs": {
      "schedule": "Month,Day,Weekday,Time,TOU\nJan-Dec,,Mon-Fri,0-8,10.48\nJan-Dec,,Mon-Fri,8-24,11.00\nJan-Dec,,Sat-Sun,0-8,9.19\nJan-Dec,,Sat-Sun,8-24,11.21"
    }
  }
}
```

!!! warning "Line breaks must be esacped in the JSON string form"

	When encoding the CSV data into a JSON string, note that line breaks should be escaped as `\n`
	for the JSON to be valid.

Alternatively, the previous example could be encoded as a JSON array-of-arrays, which would look
like this:

```json
{
  "pm": {
    "tariffs": {
      "schedule": [
		["Month","Day","Weekday","Time","TOU"],
		["Jan-Dec",,"Mon-Fri","0-8","10.48"],
		["Jan-Dec",,"Mon-Fri","8-24","11.00"],
		["Jan-Dec",,"Sat-Sun","0-8","9.19"],
		["Jan-Dec",,"Sat-Sun","8-24","11.21"]
	  ]
    }
  }
}
```
