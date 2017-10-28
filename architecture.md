# Architecture

Figure 1 shows the high-level architecture of the platform, with the most important components and communication between components. The central component is the orchestrator, which is responsible for the deployment of services and initializing connections between instances of services. The orchestrator exposes an API that is used by the web-based user interface. This approach allows third-party developers to incorporate the orchestrator by using the API. Services are stored in a central repository. The services are stored as Docker images, with additional metadata attached to the images describing the interfaces that service is able to receive connections on. The description of interfaces occurs via either Protocol Buffers \(_protobuf_\) or XML Schema Definition \(_XSD_\), this way the compatibility of interfaces is ensured when two interfaces have compatible definitions.

Nodes in the environment are hosts that are capable of running the Docker images as containers. The nodes are attached to a Virtual Private Network \(_VPN_\) in order to have a secured private network in which processes are able to communicate with each other, as well as processes being able to receive directions from the orchestrator.

![&quot;High-level overview&quot;](/assets/highlevel-overview.png)

| Component | Description |
| --- | --- |
| _Orchestrator_ | Orchestrates the runtime environment of processes and connections. Exposes an API to control the current environment. |
| _Services_ | Implementations of a service that is able to be deployed by the orchestrator as process. Contained in an image with additional metadata describing the properties and interfaces of that service. |
| _Interfaces_ | Description of the messages a service is able to send and receive in a standardised way with Protocol Buffers \(protobuf\) or XML Schema Definitions \(XSD\). |
| _Repository_ | Repository containing services with their metadata, also allowing different versions of the same service to be deployable. |
| _Processes_ | Instantiated services in the runtime environment, managed by the orchestrator. |
| _Connections_ | Link between compatible interfaces of two processes. |
| _Nodes_ | Nodes in the cluster on which processes can be deployed. Are either limited to one user \(_private_\) or are available for all users \(_public_\) to deploy instances of services on. |
| _Web-based user interface_ | User interface communicating with the orchestrator API to manage the runtime environment for users. |



