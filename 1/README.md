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

Describe standard message for Waltz-Controls

### Message standard [raw]

Waltz message wraps any upstream endpoint messages into the following envelope:

```json
{
  "id":"string|number[optional, but desired]",
  "origin":"string[required]",
  "format":"string[optional, but desired]",
  "parentId": "string|number[optional]",
  "target":"string[optional]",
  "user":"object[optional]",
  "payload":"object[optional]"
}
```

`id` MAY NOT be specified, otherwise respresents quazi unique id of this message meaning implementation MAY NOT force uniqueness within Waltz-Controls messaging system. As first approach impelementation MAY use timestamps to set `id`.

`parentId` MAY NOT be specified, otherwise is the `id` of the parent message. Useful to build chains of messages e.g. user reads value, gets response, executes command etc

`target` MAY NOT be specified in this case the system (Waltz-Controls) will do the best to deliver this message to all endpoints. Each endpoint will then act according to  the `origin` field.

`origin` MUST BE specified. Indicates a place of origin of this message e.g. `tango`, `dataforge`, `doocs` etc. This field represents unique endpoint within Waltz-Controls system.

`format` MAY NOT be set specified in this case considered to be the same as `origin`. Specifies the format of payload block. Useful in case multiple endpoints with the same type connected to the loop.

`user` MAY NOT be set, otherwise - the following structure:

```json
{
  "name":"string",
  "auth":"OAuth2|Basic|Token",
  "password":"string"
}
```

`payload` any specific upstream endpoint data MUST be serialized into JSON object(s) and stored in the `payload` field. Payload content SHOULD be specified in a dedicated RFCs, see #6, #7, #8, #9


### Examples

Minimal valid message:

```json
{
  "origin":"magix",
  "payload": "heartbeat"
}
```

Tango-Controls read attribute response message:

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

Controls.kt write property:

```json
{
  "id": 1235,
  "origin": "waltz",
  "format": "dataforge",
  "target": "192.168.111.132:8882",
  "payload":{
    "type": "property.set",
    "targetDevice":"my-device",
    "property": "a",
    "value": 11,
    "comment": "pretty please!"
  }
}
```

### References

[1] [Tango REST API](https://github.com/tango-controls/rest-api)
