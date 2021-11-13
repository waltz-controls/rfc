---
shortname: 6/AXSIS
name: AXSIS XES message standard
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

Describe standard message for AXSIS XES slow control system within Waltz-Controls eco-system

### Message standard [raw]

**channel**

All the messages MUST be sent inside `axsis-xes` channel

**origin**

AXSIS XES backend MUST respond with `"origin": "axsis"`

AXSIS XES GUI MAY respond with `"origin":"axsis-gui"`

AXSIS XES virtual Tango host/server MAY respond with `"origin":"axsis-tango"`

**payload**

Backend MUST respond with the standard Magix message, see [RFC-1](1) with the following payload: 

```
{
  "ip":[string],
  "port":[int],
  "action":[string: "MOV"|"qPOS"|"done"|"error"],
  "value": [object: motorId -> position]
}
```



**full message example**

Sending MOV command to a PI controller:

```json
{
  "id":1636791022543,
  "origin":"axsis-gui",
  "target":"axsis",
  "payload": {
    "ip":"192.168.0.103",
    "port":50000,
    "action":"MOV",
    "value": {
      "1":12.000000100000001,
      "3":12.000000100000001,
      "5":12.4000001
    }
  }
}
```
