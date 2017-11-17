# Java tutorial

The code generator for a Java service uses a Maven plugin that is setup in the `service-parent` artifact of the `org.flexiblepower.defpi` group. For this tutorial the `service.json` of [Development process](development-process.md) is used. 

This tutorial is based on the [tutorial repository](https://github.com/flexiblepower/defpi-tutorial). In the steps the simple universal dimmer will be discussed, this service will curtail a simulated light via EFI.

## Step 1: Project layout

The structure of the project must be as follows:

```
├── pom.xml
└── src
    └── main
        └── resources
            └── service.json
```

The service description has to be placed in `src/main/resources`, which is also the relative path for local XSD or Protocol Buffer files.

The pom.xml file contains the maven description for the project, which inherits from the `service-parent` project. To be able to push the service to a registry, the `docker.registry` property should be set to a Docker registry you are able to push Docker images to. Following the tutorial on [Running dEF-Pi on a local Linux machine](getting-def-pi-up-and-running/running-def-pi-on-linux.md) the local hosted registry that was deployed is used.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>org.flexiblepower.defpi</groupId>
    <artifactId>service-parent</artifactId>
    <version>17.11</version>
  </parent>

  <artifactId>universal-dimmer</artifactId>
  <version>0.0.1</version>

  <properties>
    <docker.registry>localhost:5000</docker.registry>
  </properties>
</project>
```

## Step 2: Code generation

The code generation only executes on demand, it is not bound by default to a maven phase. The reason is to avoid overwriting code, in case the user wants to change interface, _even though this is not recommended_! To use the code generation first prepare a valid service.json file and then run the generate Maven goal, as follows:

```bash
mvn defpi:generate
```

The maven plugin will then create the following project structure:

```
├── pom.xml
├── src
│   └── main
│       ├── java
│       │   └── org
│       │       └── flexiblepower
│       │           └── defpi
│       │               └── universaldimmer
│       │                   ├── inflexible_controller
│       │                   │   ├── efi_20
│       │                   │   │   └── InflexibleController_efi20ConnectionHandlerImpl.java
│       │                   │   └── InflexibleControllerConnectionManagerImpl.java
│       │                   └── UniversalDimmer.java
│       └── resources
│           ├── defpi-resources
│           └── service.json
└── target
    ├── generated-resources
    │   └── defpi
    │       ├── docker
    │       │   └── Dockerfile
    │       ├── docker-arm
    │       │   └── Dockerfile
    │       └── xsd
    │           └── InflexibleController_efi20.xsd
    └── generated-sources
        └── java
            └── org
                └── flexiblepower
                    └── defpi
                        └── universaldimmer
                            └── inflexible_controller
                                ├── efi_20
                                │   ├── InflexibleController_efi20ConnectionHandler.java
                                │   └── xml
                                │       ├── ActuatorBehaviour.java
                                │       ├── ...
                                │       └── Transitions.java
                                └── InflexibleControllerConnectionManager.java
```

In `src/main/java` the stubs for implementing the connection managers and the connection handlers are placed. 

The files placed in the `target` folder are generated files that should not be altered by service developers. Containing the `Dockerfile`s and the XSD or Protocol Buffer files, but also the Java interfaces and the XSD or Protocol Buffer bindings for Java.

> NOTE: Make sure when importing a dEF-Pi service in an IDE to add the `target/generated-sources` folder as \(generated-\)sources folder. This ensures the IDE knows it has to include the sources in this file.

Once the interface message is also defined, running the `maven-plugin` maven plugin to generate the service stub also generates the interface messages, Connection Handler interface and the Connection Handler implementation stub. Bear in mind that running the plugin again will overwrite all files in the `target/generated-sources` folder, the stubs are only regenerated if the class was not implemented already.

## Step 3: Extending the stub

Two kind of stubs are generated for implementing a service: the service implementation, and the connection handlers.

### Step 3.1: Service

One service implementation stub is generated per service, for the service.json used in the previous step this is `src/main/java/org/flexibilepower/defpi/universaldimmer/UniversalDimmer.java`.

The methods in this stub are the following:

```java
public void resumeFrom(Serializable state)
```
This method resumes the state of a previous process via an `Serializable` state. This method corresponds with the `suspend()` method, which creates the serializable state for that process. Any state related data should be stored, so that via this method the state can be reapplied to this process.

```java
public void init(EchoServiceConfiguration config, DefPiParameters parameters)
```
This method initializes a process with a configuration `EchoServiceConfiguration` object following the parameters as set in the `service.json`. Information of the orchestrator and the user that instantiated this service is retrievable via the DefPiParameters object. 


```java
public void modify(EchoServiceConfiguration config)
```
This method allows users to update the configuration of a running process via the orchestrator.

```java
public Serializable suspend()
```
This method should provide all state related variables in a Serializable object, so that in can be send to the orchestrator.

```java
public void terminate()
```
This method is called when the orchestrator indicates that the process should terminate, allowing the process to terminate safely.

The Universal Dimmer is a very simple example, and does not require a configuration and does not have a state to be saved. So the all the stubs in this case remain empty methods.

### Step 3.2: ConnectionHandlers

For each interface, as described in the `service.json` file, a ConnectionManager implementation is generated. This connection manager provides the constructors of the connection handlers for that service, one connection handler is generated for each version of the interface.

The Connection Handler Implementation class generated by the plugin provides stub implementations of five methods:

```java
public void handleMMessage(M message)
```
This method is invoked when a message of type **M** arrives. Here **M** does not refer to a generic parameter but a message type defined by the developer in the service description. For every message supported by an interface, a `handleMMsg()` stub is generated.

```java
public void onSuspend()
```
This method is invoked when a connection is suspended. Please note that this refers to the suspended state of the connection and not the process. A connection may be suspended so as to resume at a future time. For example, when a process is migrated to another node, the orchestrator suspends the process' connections along with the process itself. The orchestrator then resumes the process and its connections.

```java
public void resumeAfterSuspend()
```
This method is invoked when a connection is resumed **after the connection was suspended**. As with `onSuspend()`, this refers to the state of the connection and not the process itself.

```java
public void onInterrupt()
```

This method is invoked when the process' connection is interrupted. This could happen if the physical network is disrupted or the underlying docker networking framework fails.


```java
public void resumeAfterInterrupt()
```
Upon interruption, either the service library or the orchestrator attempts to resume the connection at a future time. This method is then invoked.

```java
public void terminated()
```
Upon interruption, either the service library or the orchestrator attempts to resume the connection at a future time. This method is then invoked.

For the Universal Dimmer, only the methods for handling messages are implemented, as there are no extra logic is needed for managing the connection.

The messages that are handled are EFI messages, especially the `InflexibleCurtailmentOptions` message. In this case the dimmer receives the curtailment options of a simulated light, and will create an instruction to curtail the light to the lower bound of the curtailment option.

```java
@Override
public void handleInflexibleRegistrationMessage(final InflexibleRegistration message) {
    System.out.println("Received registration of " + message.getDeviceDescription().getLabel());
}

@Override
public void handleMeasurementMessage(final Measurement message) {
    System.out.println("Measurement value received: " + message.getElectricityMeasurement().getPower());
}

@Override
public void handleInflexibleCurtailmentOptionsMessage(final InflexibleCurtailmentOptions message) {
    try {
        final double minimalPower = message.getCurtailmentOptions()
                .getCurtailmentOption()
                .get(0)
                .getCurtailmentRange()
                .get(0)
                .getLowerBound();

        final CurtailmentProfileElement curtailMent = new CurtailmentProfileElement().withValue(minimalPower)
                .withDuration(DatatypeFactory.newInstance().newDuration(Duration.ofDays(1).toMillis()));

        final CurtailmentProfile profile = new CurtailmentProfile().withStartTime(message.getValidFrom())
                .withCurtailmentQuantity(CurtailmentQuantity.ELECTRICITY_POWER)
                .withCurtailmentProfileElement(curtailMent);

        final InflexibleInstruction instruction = new InflexibleInstruction().withEfiVersion("2.0")
                .withInstructionId(UUID.randomUUID().toString())
                .withCurtailmentProfile(profile);

        this.connection.send(instruction);
    } catch (final DatatypeConfigurationException e) {
        throw new RuntimeException(e.getMessage());
    }
}
```

## Step 4: Packaging & deploying

A fat JAR is created using the maven-assembly-pluing to make sure all dependencies are available in one JAR file. The docker-maven-plugin from Spotify is used to actually build and push the image. 

By executing 
```bash
mvn package
```
the FAT jar is created and the docker images are build.

The docker images can then be pushed to the registry by executing the `mvn deploy`, via the `docker.registry` property in the pom file or by using `-Ddocker.registry=REGISTRY_URL` the Docker registry that should be used for the deployment can be set. 

For example:

```bash
mvn deploy -Ddocker.registry=localhost:5000
```
