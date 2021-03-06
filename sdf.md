# A Simple Definition Format for One Data Model definitions

## Introduction

The Simple Definition Format is a format for domain experts to use in the creation and maintenance of OneDM definitions.

OneDM tools convert this format to database formats and other serializations as needed.

This document describes definitions of OneDM Objects and their associated Events, Actions, Properties, and Data types.

The JSON format of an SDF definition is described in this document.

## Example Definition:
```
{
  "info": {
    "title": "Example file for ODM Simple JSON Definition Format",
    "version": "20190424",
    "copyright": "Copyright 2019 Example Corp. All rights reserved.",
    "license": "http://example.com/license"
  },
  "namespace": {
    "st": "http://example.com/capability/odm"
  },
  "defaultNamespace": "st",
  "object": {
    "Switch": {
      "property": {
        "value": {
          "type": "string",
          "enum": ["on", "off"]
        }
      },
      "action": {
        "on": {},
        "off": {}
      }
    }
  }
}
```

## SDF structure

A SDF definition file has two sections, the information block and the definitions section.

### Information block
The information block contains generic meta data for the file itself and all included definitions.

The keyword that defines an information block is "info". It contains a set of key-value pairs that represent qualities that apply to the included definition.

Qualities of the information block are shown in the following table.

| Quality | Type | Optional | Description |
|---|---|---|---|
|title|string|no|A short summary to be displayed in search results, etc.|
|version|string|no|The incremental version of the definition, format TBD|
|copyright|string|no|Link to text or embedded text containing a copyright notice|
|license|string|no|Link to text or embedded text containing license terms|

### Definitions block

The Definitions block contains the namespace and default namespace declarations.

The namespace declaration is a map containing one or more definitions of short names for URIs.

The defaultnamespace declaration defines one of the short names in the namespace map to be the default namespace.

| Quality | Type | Optional | Description |
|---|---|---|---|
|namespace|map|yes|Defines short names mapped to namespace URIs, to be used as identifier prefixes|
|defaultnamespace|string|yes|Identifies one of the prefixes in the namespace map to be used as a default in resolving identifiers|

The following example declares a set of namespaces and defines  "st"" as the default namespace.
```
"namespace": {
  "st": "http://example.com/capability/odm",
  "zcl": "http://example.com/zcl/odm"
},
"defaultnamespace": "st",
```
The definitions block contains one or more type definitions according to the class name keywords for type definitions (object, property, action, event, data).

Each class may have zero or more type definitions associated with it. Each defined identifier creates a new type and term in the target namespace, and has a scope of the current definition block.

A definition consists of a map entry using the newly defined term as a JSON keyword, with a value consisting of a map of Qualities and their values.

A definition may in turn contain other definitions. Each definition consists of the newly defined identifier and a set of key value pairs that represent the defined qualities and contained type definitions.

For example, an Object definition looks like this:

```
"object": {
  "foo": {
    "id": 3001,
    "property": {
      "bar": {
        "type": "boolean"
        "id": 5150
      }
    }
  }
}
```
An Object "foo" is defined in the default namespace, with an ID of 3001, containing a property "foo.bar", with an ID of 5150 and of type boolean.

## Scope of Identifiers
### Namespace resolution order:
1. identifiers with an explicit namespace prefix
2. keywords and quality names in the ODM and JSON Schema namespaces, defined in
the JSON Schema for SDF
3. identifier defined in the same (local) block
4. identifier defined in the next closest enclosing block recursively
5. identifier defined in the file
6. identifier defined in the default namespace

### Identifier name expansion from JSON path

When a type is defined, the identifier is internally prefixed with the JSON path that locates the definition in the file.

Types defined at the top level in the file go into the namespace as they are.

Types defined within another definition are prefixed with the path to the enclosing definition. For example, if there is an Object "foo" defined, and a definition for "bar" with Object foo, the path to bar will be "foo.bar" within the default namespace. The Property name "foo.bar" can be used as a reference within some other definition, provided that the scope can be correctly resolved.

### Target namespace

The target namespace is the namespace into which the defined terms are added. The target namespace is defined by the default namespace, or by an explicit prefix on the identifier separated by a semicolon ":".

For example if the default namespace in the example above is "baz", then you could refer to "baz:foo.bar" to point to the property defined by the "bar" definition.

## Keywords for type definitions

The following SDF keywords are used to create type definitions in the target namespace.

### Object

The object keyword denotes zero or more Object definitions. A object may contain or include definitions of events, actions, properties, and data types.

- Qualities of Object

| Quality | Type | Optional | Description |
|---|---|---|---|
|id| integer, string | yes | internal unique identifier for the definition |
|name|string|yes|human readable name|
|description|string|yes|human readable description|
|title|String|yes|human readable title to display|
|optional| boolean|yes|defines whether this element is optional in an implementation|
|includes|string|yes|reference to a definition to be included|
|refines|string|yes|reference to a definition to be refined|
|extends|string|yes|reference to a definition to be extended|

- Types Object may contain

|Type|
|---|
|property|
|action|
|event|
|data|


### Property

The property keyword denotes zero or more property definitions.

Properties are used to model elements of state.

- Qualities of Property

| Quality | Type | Optional | Description |
|---|---|---|---|
|id| integer, string | yes | internal unique identifier for the definition |
|name|string|yes|human readable name|
|description|string|yes|human readable description|
|title|String|yes|human readable title to display|
|optional| boolean|yes|defines whether this element is optional in an implementation|
|includes|string|yes|reference to a definition to be included|
|refines|string|yes|reference to a definition to be refined|
|extends|string|yes|reference to a definition to be extended|
|readOnly|boolean|yes|Only reads are allowed|
|writeOnly|boolean|yes|Only writes are allowed|
|observable|boolean|yes| flag to indicate asynchronous notification is available|
|contentFormat|string|yes|IANA media type string|
|units|string|yes|UCUM unit code|
|nullable|boolean|yes|indicates a null value is available for this type|
|encoding|map|yes|applies additional constraints|
|scaleMinimum|number|yes|lower limit of value in units|
|scaleMaximum|number|yes|upper limit of value in units|
|type|string, enum|yes|JSON data type|
|minimum|number|yes|lower limit of value in the representation format|
|maximum|number|yes|upper limit of value in the representation format|
|multipleOf|number|yes|indicates the resolution of the number in representation format|
|enum|array|yes|enumeration constraint|
|pattern|string|yes|regular expression to constrain a string pattern|
|minLength|integer|yes|shortest length string in octets|
|maxLength|integer|yes|longest length string in octets|
|default|number, boolean, string|yes|specifies the default value for initialization|
|const|number, boolean, string|yes|specifies a constant value for a data item or properety|


- Types Property may contain

|Type|
|---|
|data|

### Action

The action keyword denotes zero or more Action definitions.

Actions are used to model commands and methods which are invoked. Actions have parameter data that are supplied upon invocation.

- Qualities of Action

| Quality | Type | Optional | Description |
|---|---|---|---|
|id| integer, string | yes | internal unique identifier for the definition |
|name|string|yes|human readable name|
|description|string|yes|human readable description|
|title|String|yes|human readable title to display|
|optional| boolean|yes|defines whether this element is optional in an implementation|
|includes|string|yes|reference to a definition to be included|
|refines|string|yes|reference to a definition to be refined|
|extends|string|yes|reference to a definition to be extended|

- Types Action may contain

|Type|
|---|
|data|


### Event

The event keyword denotes zero or more Event definitions.

Events are used to model asynchronous occurences that may be communicated proactively. Events have data elements which are communicated upon the occurence of the event.

- Qualities of Event

| Quality | Type | Optional | Description |
|---|---|---|---|
|id| integer, string | yes | internal unique identifier for the definition |
|name|string|yes|human readable name|
|description|string|yes|human readable description|
|title|String|yes|human readable title to display|
|optional| boolean|yes|defines whether this element is optional in an implementation|
|includes|string|yes|reference to a definition to be included|
|refines|string|yes|reference to a definition to be refined|
|extends|string|yes|reference to a definition to be extended|
 readable title to display|

- Types Event may contain

|Type|
|---|
|data|


### Data

The data keyword denotes zero or more Data type definitions.

A Data type definition provides a semantic identifier for a data item and describes the constraints on the defined data item.

- Qualities of Data

| Quality | Type | Optional | Description |
|---|---|---|---|
|id| integer, string | yes | internal unique identifier for the definition |
|name|string|yes|human readable name|
|description|string|yes|human readable description|
|title|String|yes|human readable title to display|
|optional| boolean|yes|defines whether this element is optional in an implementation|
|includes|string|yes|reference to a definition to be included|
|refines|string|yes|reference to a definition to be refined|
|extends|string|yes|reference to a definition to be extended|
|units|string|yes|UCUM unit code|
|nullable|boolean|yes|indicates a null value is available for this type|
|encoding|map|yes|applies additional constraints|
|scaleMinimum|number|yes|lower limit of value in units|
|scaleMaximum|number|yes|upper limit of value in units|
|type|string, enum|yes|JSON data type|
|minimum|number|yes|lower limit of value in the representation format|
|maximum|number|yes|upper limit of value in the representation format|
|multipleOf|number|yes|indicates the resolution of the number in representation format|
|enum|array|yes|enumeration constraint|
|pattern|string|yes|regular expression to constrain a string pattern|
|minLength|integer|yes|shortest length string in octets|
|maxLength|integer|yes|longest length string in octets|
|default|number, boolean, string|yes|specifies the default value for initialization|
|const|number, boolean, string|yes|specifies a constant value for a data item or properety|

- Types Data may contain

|Type|
|---|
|N/A|


## Example Definition:
```
{
  "info": {
    "title": "Example file for ODM Simple JSON Definition Format",
    "version": "20190424",
    "copyright": "Copyright 2019 Example Corp. All rights reserved.",
    "license": "http://example.com/license"
  },
  "namespace": {
    "st": "http://example.com/capability/odm"
  },
  "defaultNamespace": "st",
  "object": {
    "Switch": {
      "property": {
        "value": {
          "type": "string",
          "enum": ["on", "off"]
        }
      },
      "action": {
        "on": {},
        "off": {}
      }
    }
  }
}
```
