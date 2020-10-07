---
shortname: 3/DF
name: DataForge-control message standard
status: raw
editor: Alexander Nozik (nozik.aa@mipt.ru)
---

## Preamble

Copyright (c) 2020 Waltz-Controls.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).


### Envelope API

The DataForge device message follows the DataForge Envelope API and in general could contain two parts:
1. Human-readable value tree called meta
2. A binary block called data

The Envelope API does not impose limits on specific coding of meta. It is inferred based on specific transport protocol.

### Device message schema

The device message (in JSON representation) looks following way:

```json
{
  "action": "string[required]",
  "status": "string[optional, default = 'OK']",
  "sourceName": "string[optional]",
  "targetName": "string[optional]",
  "comment": "string[optional]",
  "key": "string[required for some actions]",
  "value": "any[required for some actions]"
}
```

`action` describes the aim of the message. The pre-defined values are:
* `read`. Read a property value. `key` field is required.
* `write` Write a property value. Both `key` and `value` are required.
* `execute` Execute an action on device. `key` field is required.
* `propertyList` Return a key-value pair of property descriptors.
* `actionList` Return a key-value pair of action descriptors.

The response action follows `action.${request.action}` pattern.

`status` currently supports `OK` and `FAIL` statuses. Status could be more detailed in future. A message with `FAIL` status is not required to have valid `value`.

`sourceName` name of message source device node following DataForge name pattern (tokens, separated by `.`). The device name is unique within single DataForge device hub.

`targetName` name of message target device node following DataForge name pattern (tokens, separated by `.`). The device name is unique within single DataForge device hub.

`comment` optional message comment. Used to pass human-readable errors.

`key` a property key or action name.

`value` a string/boolean/number/object value used as a property value or as action argument/result.

### Device message as Waltz message payload

When used inside Waltz protocol, DataForge-devices are coded as Json and are put inside `payload` field (see [1](../1)). Binary part of DataForge envelope is not currently supported in Waltz.