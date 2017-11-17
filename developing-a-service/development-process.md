# Development process

Developing services for dEF-Pi share a common way of describing a the service, containing information on the interfaces a service describes. On this page the common parts of developing a service are discussed in tutorial format. At this moment there is support for implementing services in [Java](java-tutorial.md), the supported languages will be extended in the future.

## Step 1: Interface Definition

Creating a service begins with describing interfaces in the Service Descriptions (`service.json`). 

```json
{
  "name": "Universal Dimmer",
  "version": "2017",
  "interfaces": [
    {
      "name": "Inflexible Controller",
      "autoConnect": true,
      "allowMultiple": true,
      "interfaceVersions": [
        {
          "versionName": "efi 2.0",
          "type": "xsd",
          "location": "https://raw.githubusercontent.com/flexiblepower/efi/master/schema/efi-2.0.xsd",
          "receives": [
            "InflexibleRegistration",
            "InflexibleCurtailmentOptions",
            "Measurement"
          ],
          "sends": [
            "InflexibleInstruction"
          ]
        }
      ]
    }
  ]
}
```

The supported fields for services are:

| Field | Values | Description |
|:---:|:---:|---|
| _name_ | String \(required\) | The name of the service |
| _version_ | String \(required\) | The version descriptor of the service |
| _parameters_ | JSON Array | Parameters that can be set when instantiating a service |
| _interfaces_ | JSON Array | Collection of interfaces supported by this service |


The supported field for parameters are:

| Fields | Values | Description |
|:---:|:---:|---|
| _id_ | String \(required\) | The identifier of the parameter, starting with a lowercase letter and containing only letters and numbers |
| _name_ | String \(required\) | The name of the parameter | 
| _type_ | Enum \(required\) | The type of the paramer, with the following options: `boolean`, `byte`, `character`, `short`, `integer`, `long`, `float`, `double`, `string`. |
| _isArray_ | Boolean | Is the parameter an array? |
| _isOptional_ | Boolean | Is the parameter optional? |
| _values_ | String Array | The allowed values of the parameter. |
| _labels_ | String Array | The labels for the allowed values of the parameter. |



The supported fields in describing an interface are:

| Field | Values | Description |
|:---:|:---:|---|
| _name_ | String \(required\) | The name of the interface |
| _autoConnect_ | Boolean \(required\) | Connect automatically to mirror interfaces? |
| _allowMultiple_ | Boolean \(required\) | Permit more than one connections to connect to this interface? |
| _interfaceVersions_ | JSON Array | Collection of interface versions supported by this service. |

The supported fields in describing an interface version are:

| Field | Values | Description |
|:---:|:---:|---|
| _versionName_ | String \(required\) | The version descriptor of the interface |
| _type_ | String (`proto` &#124; `xsd`) \(required\) | The format in which the interface messages are defined - As [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) or as [XML Schema Definition](https://www.w3schools.com/xml/schema_intro.asp)|
| _location_ | String \(required\) | The name of the `.proto` or `.xsd` file with the definitions of interface messages, if it resides in the same directory as the `service.json` file. Otherwise, include the full path to the file in this value. |
| _sends_ | String Array \(required\) | The list of names of the supported interface messages defined in the above file, outbound from the service |
| _receives_ | String Array \(required\)| The list of names of the supported interface messages defined in the above file, inbound to the service |

## Step 2: Interface Message Definition
The developer can define interface messages as either Protocol Buffers or XML Schema Definitions in a file specified in the interface version description's *type* and *location* fields. Either way, the code generator will choose an appropriate compiler and create an implementation for these messages.

An example of the interface message definition is shown below, where an XSD version and a Protocol Buffers version of the same message definition are shown.
### XML
```xml
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
            targetNamespace="urn:books"
            xmlns:bks="urn:books">

  <xsd:element name="books" type="bks:BooksForm"/>

  <xsd:complexType name="BooksForm">
    <xsd:sequence>
      <xsd:element name="book" 
                  type="bks:BookForm" 
                  minOccurs="0" 
                  maxOccurs="unbounded"/>
      </xsd:sequence>
  </xsd:complexType>

  <xsd:complexType name="BookForm">
    <xsd:sequence>
      <xsd:element name="author"   type="xsd:string"/>
      <xsd:element name="title"    type="xsd:string"/>
      <xsd:element name="genre"    type="xsd:string"/>
      <xsd:element name="price"    type="xsd:float" />
      <xsd:element name="pub_date" type="xsd:date" />
      <xsd:element name="review"   type="xsd:string"/>
    </xsd:sequence>
    <xsd:attribute name="id"   type="xsd:string"/>
  </xsd:complexType>
</xsd:schema>
```

### Protobuf
```protobuf
message Book {
  required string id = 1;
  required string author = 2; 
  required string title = 3; 
  required string genre = 4; 
  required string price = 5; 
  required string pub_date = 6; 
  required string review = 7; 
}

message Books {
  optional repeated Book books = 1;
}
```

## Step 3: Code generation

With a correct Service Description, the dEF-Pi code generation tools are able to create the basic structure of the service. This includes code for the interfaces described in the Service Description, creating a compilable code base in which only the interfaces have to be implemented.

Depending on the programming language to use for the service go to:

* [Java Tutorial](java-tutorial.md)
