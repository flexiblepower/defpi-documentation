# Build and deploy tools

The dEF-Pi project provides build and deploy tools for developers to easily create new services compatible with the dEF-Pi environment. By specifying requirements of a service in a simple JSON notation, code can be generated for Java. This relieves developers from worrying about the dEF-Pi specific code and keeps focus on the implementation of the logic for the interfaces.

An example of such service description is as follows:
```JSON
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

This describes a universal dimmer that is able to curtail devices via the EFI protocol. In [Developing a Service](../developing-a-service.md) the complete explaination can be found on how to use the service description.

Besides the code generation, dEF-Pi also provides deploy tools to easily deploy a test environment via docker-compose. Making sure that all dependencies are deployed and configured correctly. More information on the deploy tools can be found in [Getting dEF-Pi up and running](../getting-def-pi-up-and-running.md). 