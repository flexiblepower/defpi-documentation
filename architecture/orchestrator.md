# Orchestrator

The orchestrator is the most important component in the architecture and is responsible for several things: the deployment of services, the initiation of connections between services, lifecycle management of processes, and user management.

The orchestrator is able to deploy services on a cluster of hosts. Either the user specifies where a service has to be deployed or the orchestrator picks one itself. When deploying a service, the end-user is able to specify which version of the service must be deployed. Providing the ability to set configuration data for each processes individually. Processes do not share these environment variables, so settings (e.g. a MAC address or an identifier) can be different for processes of the same service. This way users can deploy a generic service and customize its behavior by specifying environment variables.

The orchestrator is the only component that can initiate connections between services. This way the orchestrator is able to verify whether or not services should communicate with each other, by determining the compatibility of interfaces implemented by services. For example, when the interfaces don't match or when the cardinality will be violated no connection is created. Via the web-based user interface, end-users are able to specify connections by selecting the correct processes and interfaces to be connected. 

The orchestrator manages the lifecycle of processes deployed on the dEF-Pi environment, providing the ability to: create, update, move, and terminate processes. Also, the management of connections between processes is part of the lifecycle management. 

In order to robustly apply changes to running processes _Pending changes_ are introduced. As communication between the orchestrator and processes can't always be assured, pending changes are placed in a queue. This allows the possibility to reapply a change in case it has not been executed correctly by a process. 

As dEF-Pi is a distributed platform, the Orchestator has to manage nodes in its environment. Nodes are categorised in one of three categories:
* **Unidentified nodes**: nodes that are added to the environment, but have not been setup correctly. Therefore, these nodes can't be used to deploy processes on.
* **Public nodes**: nodes that are put in a public node pool, available for all users in the environment to deploy processes on.
* **Private nodes**: nodes tied to a single user, only available for that user to deploy processes on.

Each user in the dEF-Pi environment has its own user environment. Such user environment consists out of:
* The public nodes in the dEF-Pi environment.
* The private nodes tied to the user.
* The processes deployed by the user.
* The connections between processes of the user.

## Overview

Figure 2 shows the different components that are contained in the orchestrator. The orchestrator is split up into: the API implementation, Managers, and Connectors. The API implementation provides the interface for user to communicate with via the web-based user interface. The managers implement the logic for all the different aspects of the orchestrator. And the connectors connect to external information providers which is used by the managers.

![&quot;Orchestrator overview&quot;](/assets/orchestrator.png)

A quick overview of the managers and connectors in the orchestrator is as follows:

| Component 			| Description |
| --- 					| --- |
| REST API 				| Provides an interface to communicate with the orchestrator, via the web-based user interface. \(TODO: add reference to swagger documentation\) |
| Process Manager 		| Manages the processes in the environment. Responsible for listing, creating, updating, moving, and deleting processes. |
| Connection Manager 	| Manages connections between interfaces of processes. Responsible for listing, creating, and terminating connections. |
| User Manager 			| Provides authentication of users of the orchestrator environment.  |
| Node Manager 			| Manages nodes available to the orchestrator. Nodes are put into one of three categories: private, public, or unidentified. |
| Service Manager 		| Lists services and their interfaces published to the registry. |
| Process Connector 	| Connects to deployed processes to communicate the configuration and connections of the processes. |
| Database Connector 	| Connects to the MongoDB object store for writing and reading objects.  |
| Docker Connector 		| Connects to a Docker swarm manager for deploying new processes and managing shared networks between processes. |
| Registry Connector 	| Connects to the Docker Registry for the retrieval of services and their interfaces. |
