# Services, processes, and connections

To make sure applications can run on the runtime environment of dEF-Pi, services are used that contain the logic of these applications as well as information on they should interact with eachother. These services are deployed as processes in a containerized way, so that for each process an isolated environment is available that does not interfere with other services in the runtime environment. Services describe the interfaces on which processes can be connected to each other, making sure that services are able to handle messages send over these connections.

## Services {#services}

The shared service library implements the basic functions for services to work properly in the dEF-Pi environment. This library is extended by services with application specific logic for the interfaces it supports.

The basis of the service library is the `ServiceManager`, which handles the management communication with the orchestrator for configuration and life-cycle management. Handlers of interface implementations are retrieved at run-time, in order to have a solid basis for the services identical for all services deployable by the orchestrator.


## Processes {#processes}

Processes are deployed instances of services. Deployed in a isolated environment via Docker containerization. Processes are managed by the orchestrator, giving it the ability to move processes to other nodes available to the orchestrator and the user owning the process.

## Connections {#connections}

To provide a means of communication between processes on the dEF-Pi runtime environment, connections connect to an interface on two processes. These interfaces are strictly described via Protocol Buffer or XSD descriptions. This way the compatibility of two interfaces can be assured. 



