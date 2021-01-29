---
shortname: 3/DF
name: Controls.kt message standard
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

The message could correspond to several actions:

* **Read**. Synchronously read a property.
* **Write**. Synchronously write a property.
* **Execute**. Execute an action synchronously and return a result.
* **Information**. Request device specifications and information about properties

An example device message (in JSON representation) looks the following way:

```json
{
  "type": "property.changed",
  "property": "string[required]",
  "value": "object[required, could be null]",
  "sourceDevice": "string[required]",
  "targetDevice": "string[optional]",
  "comment": "string[optional]"
}
```
* **type**: type of the message.
* **property**: the name of the property with a changed value.
* **value**: the generic JSON tree representing the property value. Could be null if the property is invalidated.
* **sourceDevice**: the system (Controls.kt or DOOCS) name of the message source device.
* **targetDevice**: the system name of the message target device.

Current available messages in a serializable Kotlin format (using kotlinx-serialization schema generation) are the following:

```kotlin
/**
 * Notify that property is changed. [sourceDevice] is mandatory.
 * [property] corresponds to property name.
 * [value] could be null if the property is invalidated.
 *
 */
@Serializable
@SerialName("property.changed")
public data class PropertyChangedMessage(
    public val property: String,
    public val value: MetaItem<*>?,
    override val sourceDevice: String,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * A command to set or invalidate property. [targetDevice] is mandatory.
 */
@Serializable
@SerialName("property.set")
public data class PropertySetMessage(
    public val property: String,
    public val value: MetaItem<*>?,
    override val sourceDevice: String? = null,
    override val targetDevice: String,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * A command to request property value asynchronously. [targetDevice] is mandatory.
 * The property value should be returned asynchronously via [PropertyChangedMessage].
 */
@Serializable
@SerialName("property.get")
public data class PropertyGetMessage(
    public val property: String,
    override val sourceDevice: String? = null,
    override val targetDevice: String,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * Request device description. The result is returned in form of [DescriptionMessage]
 */
@Serializable
@SerialName("description.get")
public data class GetDescriptionMessage(
    override val sourceDevice: String? = null,
    override val targetDevice: String,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * The full device description message
 */
@Serializable
@SerialName("description")
public data class DescriptionMessage(
    val description: Meta,
    override val sourceDevice: String,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * A request to execute an action. [targetDevice] is mandatory
 */
@Serializable
@SerialName("action.execute")
public data class ActionExecuteMessage(
    public val action: String,
    public val argument: MetaItem<*>?,
    override val sourceDevice: String? = null,
    override val targetDevice: String,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * Asynchronous action result. [sourceDevice] is mandatory
 */
@Serializable
@SerialName("action.result")
public data class ActionResultMessage(
    public val action: String,
    public val result: MetaItem<*>?,
    override val sourceDevice: String,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * Notifies listeners that a new binary with given [binaryID] is available. The binary itself could not be provided via [DeviceMessage] API.
 */
@Serializable
@SerialName("binary.notification")
public data class BinaryNotificationMessage(
    val binaryID: String,
    override val sourceDevice: String,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * The message states that the message is received, but no meaningful response is produced.
 * This message could be used for a heartbeat.
 */
@Serializable
@SerialName("empty")
public data class EmptyDeviceMessage(
    override val sourceDevice: String? = null,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * Information log message
 */
@Serializable
@SerialName("log")
public data class DeviceLogMessage(
    val message: String,
    val data: MetaItem<*>? = null,
    override val sourceDevice: String? = null,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
 
/**
 * The evaluation of the message produced a service error
 */
@Serializable
@SerialName("error")
public data class DeviceErrorMessage(
    public val errorMessage: String?,
    public val errorType: String? = null,
    public val errorStackTrace: String? = null,
    override val sourceDevice: String,
    override val targetDevice: String? = null,
    override val comment: String? = null,
) : DeviceMessage()
```
The `SerialName` on top of the class is converted to a type field of appropriate JSON. The kotlinx-serialization is not limited to JSON, so it is possible to encode the same structures using different formats (including binaries).

### Device message as Waltz message payload

When used inside Waltz protocol, Controls.kt are coded as Json and are put inside `payload` field (see [1](../1)). Binary part of DataForge envelope is not currently supported in Waltz.