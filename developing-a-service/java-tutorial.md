# Java tutorial

The code generator for a Java service uses a Maven plugin that is setup in the `service-parent` artifact of the `org.flexiblepower.defpi` group. For this tutorial the `service.json` of [Development process](development-process.md) is used. 

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

The pom.xml file contains the maven description for the project, which inherits from the `service-parent` project.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>org.flexiblepower.defpi</groupId>
    <artifactId>service-parent</artifactId>
    <version>17.11</version>
  </parent>

  <artifactId>echo-skeleton</artifactId>
  <version>0.0.1</version>
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
│       │               └── echoskeleton
│       │                   ├── echo_interface
│       │                   │   ├── EchoInterfaceConnectionManagerImpl.java
│       │                   │   └── v001
│       │                   │       └── EchoInterface_v001ConnectionHandlerImpl.java
│       │                   └── EchoService.java
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
    │       └── proto
    │           └── EchoInterface_v001.proto
    └── generated-sources
        └── java
            └── org
                └── flexiblepower
                    └── defpi
                        └── echoskeleton
                            ├── echo_interface
                            │   ├── EchoInterfaceConnectionManager.java
                            │   └── v001
                            │       ├── EchoInterface_v001ConnectionHandler.java
                            │       └── proto
                            │           └── EchoInterface_v001Proto.java
                            └── EchoServiceConfiguration.java

```

In `src/main/java` the stubs for implementing the connection managers and the connection handlers are placed. 

The files placed in the `target` folder are generated files that should not be altered by service developers. Containing the `Dockerfile`s and the XSD or Protocol Buffer files, but also the Java interfaces and the XSD or Protocol Buffer bindings for Java.

Once the interface message is also defined, running the `create-service` maven plugin to generate the service stub also generates the interface messages, Connection Handler interface and the Connection Handler implementation stub. Bear in mind that running the plugin again over an implementation stub will overwrite any code that the developer wrote to extend the stub.

## Step 3: Extending the stub

Two kind of stubs are generated for implementing a service: the service implementation, and the connection handlers.

### Step 3.1: Service

One service implementation stub is generated per service, for the service.json used in the previous step this is `src/main/java/org/flexibilepower/defpi/echoskeleton/EchoService.java`.

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

## Step 4: Packaging & deploying

A fat JAR is created using the maven-assembly-pluing to make sure all dependencies are available in one JAR file. The docker-maven-plugin from Spotify is used to actually build and push the image. 

By executing 
```bash
mvn package
```

the FAT jar is created and the docker images are build.

The docker images can then be pushed to the registry by executing
```bash
mvn deploy
```

\(TODO: specify the docker repository\)
