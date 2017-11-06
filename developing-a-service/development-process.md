# Development process

Developing a service for dEF-Pi in either Java or Python share a common way of describing a the service, containing information on the interfaces a service describes. On this page the common parts of developing a service. The implementation of services can be found on either the [Java](java-tutorial.md) or the [Python](python-tutorial.md) page.

## Step 1: Interface Definition

Creating a service begins with describing interfaces in the Service Descriptions (`service.json`).

```json
{
  "name": "My First Service",
  "version": "1",
  "interfaces": [
    {
      "name": "Message Interface",
      "autoConnect": false,
      "allowMultiple": true,
      "interfaceVersions": [
        {
          "versionName": "1",
          "type": "proto",
          "location": "myMessagingProtocol.proto",
          "sends": ["Msg"],
          "receives": ["Msg"]
        }
      ]
    }
  ]
}
```

The supported fields in describing an interface are:

| Field | Values | Description |
|:---:|:---:|---|
| _name_ | String | The name of the interface |
| _autoConnect_ | Boolean | Connect automatically to mirror interfaces? |
| _allowMultiple_ | Boolean | Permit more than one connections to connect to this interface? |
| _interfaceVersions_ | JSON Array | Collection of interface versions supported by this service. |

The supported fields in describing an interface version are:

| Field | Values | Description |
|:---:|:---:|---|
| _versionName_ | String | The version descriptor of the interface |
| _type_ | String (`proto` \| `xsd`) | The format in which the interface messages are defined - As [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) or as [XML Schema Definition](https://www.w3schools.com/xml/schema_intro.asp)|
| _location_ | String | The name of the `.proto` or `.xsd` file with the definitions of interface messages, if it resides in the same directory as the `service.json` file. Otherwise, include path to the file in this value. |
| _sends_ | String Array| The list of names of the supported interface messages defined in the above file, outbound from the service |
| _receives_ | String Array| The list of names of the supported interface messages defined in the above file, inbound to the service |

## Step 2: Interface Message Definition
The developer can define interface messages as either Protocol Buffers or XML Schema Definitions in a file specified in the interface version description's *type* and *location* fields. Either way, the code generator will choose an appropriate compiler and create an implementation for these messages in Java or Python.

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
* [Python Tutorial](python-tutorial.md)