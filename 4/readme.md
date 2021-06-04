---
shortname: 4/TANGO
name: Tango-Controls Message Standard
status: raw
editor: Igor Khokhriakov (igor.khokhriakov@hzg.de)
---

## Preamble

Copyright (c) 2020 Waltz-Controls.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Waltz-Controls Message Standard Specification

The core feature of Waltz-Controls is ability to exchange messages beetween a number of upstream controls systems, as well as 3rd party components and GUIs in general refered as *endpoint*s. Therefore the main idea to keep in mind when designing this message specification is the following: when a field is related to a message routing or processing it goes to the upper level, otherwise i.e. when a field is an endpoint specific it goes into payload.


### Goals

Describe standard message for Tango-Controls within Waltz-Controls eco-system

### Message standard [raw]

General structure of the Tango-Controls payload looks like as following (optional fields are in square brackets):

```json
{
  "action":"read|write|exec|pipe",
  "timestamp": "int",
  "host":"tango_host",
  "device":"device name",
  "name":"attribute, command or pipe's name",
  "[value]":"attribute's value",
  "[quality]":"VALID|WARNING|ALARM",
  "[argin]":"command argin",
  "[argout]":"command argout",
  "[data]":"pipe's data",
  "[errors]":[]
}
```

Here one of the **value** and **quality** or **argin** and **argout** or **data** combinations must be included if action is either **write**, **exec** or **pipe**.
**value** and **quality** are if action is **read** or **write**.
**argin** and **argout** are if action is **exec**.
**data** -- if action is **pipe**

Omitting **value** and **quality** fields implies reading an attribute action.

In case of a **pipe** action the data should look like this:

```json
[
      {
        "name": "FirstDE",
        "value": [
          "The string"
        ]
      },
      {
        "name": "SecondDE",
        "value": [
          666
        ]
      },
      {
        "name": "ThirdDE",
        "value": [
          12
        ]
      }
    ]
```    

Omitting **data** field in the **pipe** action imples reading otherwise -- writing to the pipe.

In case of en error any optional field may be omitted while the **errors** field must be present.
Errors array element structure:

```json
{
  "reason":"root cause",
  "description": "detailed message",
  "severity":"PANIC|ALARM|WARNING",
}
```

For the envelope structure, please refer to [RFC-1](../1/README.md)

### Examples

Tango-Controls read attribute full response message:

```json
{
  "id":1234,
  "parentId":1233,
  "origin":"tango",
  "user":"tango-cs",
  "payload":
    {
      "action":"read",
      "host":"localhost:10000",
      "device":"sys/tg_test/1",
      "name":"double_scalar",
      "value":3.14,
      "timestamp":1234,
      "quality":"VALID"
   }
}
```

### Implementation notes

Implementation SHOULD return **null** if upstream value is null.
Implementation SHOULD return **undefined** or omit a field if upstream value is undefined e.g. void command argout.

### References

[1] [Tango REST API](https://github.com/tango-controls/rest-api)
