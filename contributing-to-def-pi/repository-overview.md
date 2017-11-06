# Repository overview

DEF-Pi consists out of several sub-projects that are integrated via maven. These sub-projects are:

* master
* api
* commons
* orchestrator
* service
* codegen-commons
* maven-plugin
* python-codegen

## Master

The master project contains the integration of the sub-projects, providing the ability to build all sub-projects.

Also, it contains the `service-parent` maven artifact which can be inherited by Java service implementations. 

## API

The API project contains the API specification used in the Orchestrator. Using the [Swagger framework](https://swagger.io), enabling the development accross the entire API lifecycle, including design and documentation.

The packages used in the API project are:

* `org.flexiblepower.api` describing the endpoints of the API.
* `org.flexiblepower.exceptions` describing the exceptions that can be thrown by the API.
* `org.flexiblepower.model` containing the data objects that act as input or output of the API.

## Commons

The Commons project contains code shared accross multiple sub-projects. The shared code focuses on the communication and serialization of messages between the orchestrator and processes. 

## Orchestrator

The Orchestrator project contains implements the Orchestrator, used to manage Users, Services, Processes, Connections, and Nodes.

The package structure of the orchestrator is as follows:

* `org.flexiblepower.connectors` this package contains the connectors to components outside the orchestrator, the available connectors are:
  - `DockerConnector` to connect to the Docker Engine of the Docker Swarm manager.
  - `MongoDBConnector` to connect to the MongoDB store, containing information on: _Users_, _Processes_, and _Connections_. The MongoDB store also provides a proxy to Service descriptions.
  - `ProcessConnector` to connect to running _processes_.
  - `RegistryConnector` to connect to the _Registry_
* `org.flexiblepower.orchestrator` this package contains the _UserManager_, _ServiceManager_, and _NodeManager_.
* `org.flexiblepower.orchestrator.pendingchange` this package contains the _PendingChangeManager_, responsible for robustly applying changes to processes.
* `org.flexiblepower.process` this package contains the _ProcessManager_, responsible for the lifecycle management of processes.
* `org.flexiblepower.rest` provides a REST interface implementation of the API project, in order to communicate with the _Orchestrator_.

## Service

The Service project contains the service library used for the implementation of Java services. Providing the communication with the _Orchestrator_ and integrates the implementation of a service.

\(TODO: package structure\)

## Codegen Commons

The Codegen Commons project contains the shared code for the Java and Python code generation projects. Providing the parsing of Service Descriptions \(`service.json`\) and a template engine.

## Maven Plugin

The Maven Plugin project provides the maven plugin used to generate code for Java implementations of services. 

The core of the project is the maven MOJO \(Maven plain Old Java Object\), which is the executable `defpi:generate` goal in Maven.

## Python Codegen

\(TODO: \)