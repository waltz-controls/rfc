---
shortname: 1/MSG
name: Waltz-Controls Message Standard
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

General structure of the Tango-Controls payload looks like as following:

```json
{
  "action":"read|write|exec|pipe",
  "timestamp": "int",
  "host":"tango_host",
  "device":"device name",
  "name":"attribute, command or pipe's name",
  "[value]":"attribute or pipe's value",
  "[argin]":"command argin",
  "[argout]":"command argout",
  "[quality]":"attribute's auality",
  "errors":[]
}
```

Errors array element structure:

```json
{
  "reason":"root cause",
  "description": "detailed message",
  "severity":"PANIC|ALARM|WARNING",
}
```


Here **value** and **quality** are if **action** is **read** or **write** or **pipe**.
Here **argin** and **argout** are if **action** is **exec**.

For the envelope structure, please refer to [RFC-1](1/readme.md)

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
