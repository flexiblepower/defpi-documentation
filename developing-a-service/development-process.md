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
| _type_ | String (`proto`\|`xsd`) | The format in which the interface messages are defined - As [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) or as [XML Schema Definition](https://www.w3schools.com/xml/schema_intro.asp)|
| _location_ | String | The name of the `.proto` or `.xsd` file with the definitions of interface messages, if it resides in the same directory as the `service.json` file. Otherwise, include path to the file in this value. |
| _sends_ | String Array| The list of names of the supported interface messages defined in the above file, outbound from the service |
| _receives_ | String Array| The list of names of the supported interface messages defined in the above file, inbound to the service |

## Step 2: Interface Message Definition
The developer can define interface messages as either Protocol Buffers or XML Schema Definitions in a file specified in the interface version description's *type* and *location* fields. Either way, the code generator will choose an appropriate compiler and create an implementation for these messages in Java or Python.

An example of the interface message definition is shown below, where an XSD version and a Protocol Buffers version of the same message definition are shown.
<table>
<tr>
  <th> XSD </th>
  <th> Protocol Buffer </th>
</tr>
<tr>
<td> 

<pre>
  <code><xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
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
</xsd:schema></code>
</pre>
</td>
<td>
<pre>
  <code>message Book {
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
}</code>
</pre>

</td>
</tr>
<tr>
<td colspan="2">
<center>

Table 1: Comparison of an example `Books` message implemented in XSD and Protocol Buffer

</center>
</td>
</table>

Once the interface message is also defined, running the `create-service` maven plugin to generate the service stub also generates the interface messages, Connection Handler interface and the Connection Handler implementation stub. Bear in mind that running the plugin again over an implementation stub will overwrite any code that the developer wrote to extend the stub.

> _**Under the hood:**_ <br>
_The Connection Handler Java Interface is annotated with information about the messaging interface (defined by the developer in the service description). The service library uses this information to establish an matching connection and deliver the correct messages to a running process._


