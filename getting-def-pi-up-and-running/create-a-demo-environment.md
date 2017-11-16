# Create a demo environment

For the demo environment, two services are used that are available at [dEF-Pi tutorial repository](https://github.com/flexiblepower/defpi-tutorial)

## Deploy services

If you followed the tutorial on how to get dEF-Pi running on Linux. The `HOSTNAME` environment variable is still present and contains the IP address of your machine. If this environment variable is not present run and you are running the registry on the same machine:

```
export HOSTNAME=$(hostname --ip-address)
```

Then we can deploy the tutorial services to the registry by running the following command in the defpi-tutorial folder:

```
mvn -f light-simulator/pom.xml -Ddocker.registry=$HOSTNAME:5000 deploy
mvn -f universal-dimmer/pom.xml -Ddocker.registry=$HOSTNAME:5000 deploy
```

After this is done, the services should appear in the orchestrator UI on the [services page](http://localhost:84/#/service/list).

## Deploy processes

To deploy the services as processes on the dEF-Pi environment, there must be at least one node identified as either public or private node.

To create a process go the [process page](http://localhost:84/#/process/list) and click create.

First we deploy an universal-dimmer.

Set the following values in the form:
* **User**: admin
* **Service**: universal-dimmer
* **NodePool**: Select the NodePool that you've created. _NOTE: If you've identified your node as Private, this field should remain empty_
* **PrivateNode**: Select the Private node that you've created. _NOTE: If you've identified your node as Public, this field should remain empty_

Click _Submit_ to create the process.

Now a light-simulator can be created in the same way, but now choosing the _light-simulator_ service instead. 

If all goes well, the process list will now show the two created services.

## Create connections

