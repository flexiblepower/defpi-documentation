# Build and deploy tools

The dEF-Pi project provides build and deploy tools for developers to easily create new services compatible with the dEF-Pi environment. By specifying requirements of a service in a simple JSON notation, code can be generated for both Java and Python. This relieves developers from worrying about the dEF-Pi specific code and keeps focus on the implementation of the logic for the interfaces.

An example of such service description is as follows:
```JSON
{
  "name": "Echo Service",
  "version": "0.0.1-SNAPSHOT",
  "parameters": [
  	{
  	  "id": "interval",
  	  "name": "interval",
  	  "type": "integer",
  	  "values": ["1", "2", "more"],
  	  "default": "2"
  	}
  ],
  "interfaces": [
    {
      "name": "Echo Interface",
      "autoConnect": false,
      "allowMultiple": false,
      "interfaceVersions": [
        {
          "versionName": "v0.0.1",
          "type": "proto",
          "location": "https://pastebin.com/raw/FyBHi5KA",
          "sends": ["Msg"],
          "receives": ["Msg"]
        }
      ]
    }
  ]
}

```

This describes a simple echo service that uses the interface described by the contents of the Protocol Buffer [file](https://pastebin.com/raw/FyBHi5KA). In [Contributing to dEF-Pi](contributing-to-def-pi.md) the complete explaination can be found on how to use the service description.

Besides the code generation, dEF-Pi also provides deploy tools to easily deploy a test environment via docker-compose. Making sure that all dependencies are deployed and configured correctly. More information on the deploy tools can be found in [Getting dEF-Pi up and running](../getting-def-pi-up-and-running.html). 