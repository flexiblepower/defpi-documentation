# Running dEF-Pi on a local Linux machine

This tutorial gives the opportunity to create a local dEF-Pi environment running on a single Linux machine.

## Step 1: Installing Docker

DEF-Pi runs on the Linux versions of Docker. Both the community edition as well as the enterprise edition are supported, from version 17.03 and upwards.

Install the latest version of Docker following the [Docker manual](https://docs.docker.com/engine/installation/).

## Step 2: Install docker-compose

To orchestrate the containers running on the Docker host, docker-compose is used for creating a local dEF-Pi environment. 

Install the latest version of docker-compose following the [docker-compose manual](https://docs.docker.com/compose/install/).

## Step 3: Initializing Docker Swarm mode

The Docker Swarm mode is used to create a cluster of nodes capable of communicating with eachother. Therefore, Docker Swarm mode has to be initialized on the host targeted to run the management components, like the orchestrator and the MongoDB instance. 

To initialize Docker Swarm mode execute:
```Bash
docker swarm init
```

## Step 4: Deploy local dEF-Pi environment

To deploy the local dEF-Pi environment a docker-compose file is provided, containing the following components: the orchestrator, the web-based user-interface, and a MongoDB instance.

The docker-compose file for the local environment is located at `orchestrator/docker-compose.test.yml`.

To deploy the docker-compose file execute:
```Bash
docker-compose -f orchestrator/docker-compose.test.yml up
```

Now the web-based interface is accessible at [http://localhost:84/](http://localhost:84/)

## Step 5: Categorize unidentified nodes

When deploying the local dEF-Pi environment, the host is added as unidentified node. To be able to deploy services on the node, it has to be categorized in either a public node pool or as a private node.

Unidentified nodes are visible via the _Unidentified nodes_ page of the web interface, showing information on the node and its status.

### Step 5.1: Add unidentified node as public node

In order to add a unidentified node as public node, first a public node pool should be created. So that the new node can be added to this node pool.

#### Step 5.1.1: Create public node pool

Go to the _Nodepool_ page via the menu of the web interface and click on _create_. Give a name to the nodepool and click on _submit_.

#### Step 5.1.2: Create public node

Go to the _Public nodes_ page and click _create_. Select the _unidentified node_ and the _node pool_ to add the _unidentified node_ to the created _nodepool_. 

### Step 5.2: Add unidentified node as private node

To add an unidentified node as a private node tied to a specific user, go to the _Private nodes_ page and click create. Select the unidentified node and the user and click _submit_.