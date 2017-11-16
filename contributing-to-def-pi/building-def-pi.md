# Building dEF-Pi

Maven is used to integrate all projects, and as such, the following command can be used to compile all the projects:

```bash
mvn -f master/pom.xml install
```

The project is separated in the following sub projects, that are build via the maven command. These sub-projects are:

* master
* api
* commons
* orchestrator
* service
* maven-plugin

The `pom.xml` file used to integrate all the projects can be found in the _master_ folder in the repository.

## Master

The master project contains the maven integration file, as well as the service-parent artifact used to create new services.

## Api

The API project contains the API description used in the orchestrator. The API uses [Swagger](https://swagger.io/), enabling development of the API accross the entire API lifecycle, including the design and the documentation.

The package structure of the Api is as follows:

* `org.flexiblepower.api` containing the endpoints of the API, used to create the structure of the API.
* `org.flexiblepower.exceptions` containing the exceptions that can be thrown by the API.
* `org.flexiblepower.model` containing the data objects that can act as input or output by the API.

## Commons

The Commons project contains code used accross multiple projects. Focusing on the messaging between components of dEF-Pi, with support for XSD, Protocol Buffers, and Java IO.

## Orchestrator

The Orchestrator project contains the implementation of the Orchestrator, capable of managing Users, Services, Processes, and Nodes.

* `org.flexiblepower.connectors` containing the connectors to support data sources. Currently, the available connectors are:
    - `DockerConnector` connecting to the Docker Engine, for communication with the Docker Swarm manager.
    - `MongoDbConnector` connecting to the MongoDB store. Containing information on processes, connections, and users. The MongoDB also acts as proxy for the _Registry_.
    - `ProcessConnector` connecting to running _processes_.
    - `RegistryConnector` connecting to the _Registry_.
* `org.flexiblepower.orchestrator` containing the _NodeManager_, _ServiceManager_, and the _UserManager_.
* `org.flexiblepower.orchestrator.pendingchange` containing the _PendingChangeManager_ responsible for robustly executing changes. 
* `org.flexiblepower.process` containing the _ProcessManager_, with all its capabilities of creating, moving, and terminating processes.
* `org.flexiblepower.rest` containing the REST implementation of the API project.

## Service

Contains the Service library that is used for the Java implementations of _Services_. This library handles the communication with the Orchestrator and other processes a process has connections with.

## Codegen commons

The Codegen commons project contains shared code between for the code generators, at this moment only for the Java maven plugin. This includes the parsing of a Service Description \(`service.json`\) and a template parser.

## Maven Java plugin

The Maven Java plugin contains the maven MOJO \(Maven plain Old Java Object\), which is the executable `defpi:generate` goal used to generate the Java code for a service. 