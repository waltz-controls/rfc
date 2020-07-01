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

The core feature of Waltz-Controls is ability to exchange messages bettween a number of upstream controls systems, as well as 3rd party components and GUIs.


### Goals

Describe standard message for Waltz-Controls

### Message standard [raw]

Waltz message wraps any upstream endpoint messages into the following envelope:

```json
{
  "id":"string|number",
  "parent": "string|number",
  "target":"string",
  "source":"string",
  "user":"string",
  "action":"string",
  "payload":"array"
}
```

`parent` id of the parent message.

`target` MAY NOT be specified in this case the system (Waltz-Controls) will do the best to deliver this message to all endpoints. Each endpoint will then act according `source` field.

`user` MAY NOT be set, otherwise - username.

`action` MAY NOT be specified. If specified contains arbitrary value that best express the naure of the message.

Any specific upstream endpoint data MUST be parsed into JSON object(s) and stored in `payload` field. For Tango-Controls such object is defined in TangoREST API [1]


### Examples

Tango-Controls read attribute message:

```json
{
  "id":1234,
  "parent":1233,
  "source":"tango",
  "user":"tango-cs",
  "action":"read",
  "payload":[
    {
      "host":"localhost:10000",
      "device":"sys/tg_test/1",
      "name":"double_scalar",
      "value":3.14,
      "timestamp":1234,
      "quality":"VALID"
   }
 ]
}
```

Dataforge:

```json
{
  "id": 1235,
  "source": "dataforge",
  "payload":[
    {
      "name": "a",
      "value": "11"
    },
    {
      "name": "b",
      "value": false
    }
  ]
}
```

### References

[1] [Tango REST API](https://github.com/tango-controls/rest-api)
