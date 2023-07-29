---
shortname: 5/DOOCS
name: DOOCS Message Standard
status: raw
editor: Alexander Nozik (altavir@gmail.com)
---

## Preamble

Copyright (c) 2020 Waltz-Controls.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Waltz-Controls Message Standard Specification

The core feature of Waltz-Controls is ability to exchange messages beetween a number of upstream controls systems, as well as 3rd party components and GUIs in general refered as *endpoint*s. Therefore the main idea to keep in mind when designing this message specification is the following: when a field is related to a message routing or processing it goes to the upper level, otherwise i.e. when a field is an endpoint specific it goes into payload.


### Goals

Describe standard message for DOOCS within Waltz-Controls eco-system

### Message standard \[raw\]

General structure of the DOOCS payload looks like as following (optional fields are in square brackets):

```json
{
  "action":"get|set",
  "eq_address": "string",
  "eq_data": {
    "type_id": "int",
    "type": "[string]",
    "value": "object|value",
    "event_id": "[int]",
    "error": "[int]",
    "time": "[long]",
    "comment": "[string]"
  }
}
```

* `eq_address` - String representation of [EqAddress](https://ttfinfo.desy.de/DOOCSWiki/Wiki.jsp?page=C%2B%2B%20Client%20Interface#section-C_2B_2B+Client+Interface-EqAdrClass) class
* `eq_data` - json representation of [EqData](https://ttfinfo.desy.de/DOOCSWiki/Wiki.jsp?page=C%2B%2B%20Client%20Interface#section-C_2B_2B+Client+Interface-EqDataClass) content. Could be omitted for get operations.
  * `type_id` - numeric constant corresponding to DOOCS `EqData` type code.
  * `type` - optional string representation of DOOCS `EqData` type. Used only for human readability.
  * `value` - an object or value representing data in the message.
  * `event_id` - DOOCS `EventId` if present
  * `error` - DOOCS error code if present
  * `time` - DOOCS timestamp if present
  * `comment` - a human-readable debug message. Is not passed to DOOCS


### References

[1] [DOOCS documentation](https://ttfinfo.desy.de/DOOCSWiki/Wiki.jsp?page=DOOCS%20Wiki)
