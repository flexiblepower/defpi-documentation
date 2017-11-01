# Create a cluster

This tutorial shows how to add new nodes to the local dEF-Pi environment, as setup in [Running dEF-Pi on a local Linux machine](running-def-pi-on-linux.md).

New nodes have to be added to the same Docker Swarm as the node running the orchestrator is in. For this a token is required which can be acquired at the machine that initialized the Docker Swarm:

```Bash
docker swarm join-token worker
```

This returns a command like:
```Bash
docker swarm join --token SWMTKN-1-XXX-XXX IPADDRESS:PORT
```

Important for this command to work is that the shown IP address is accessibly by the host you want to add to the dEF-Pi environment.

When the node is correctly added to the dEF-Pi environment, it should appear in the web interface on the _Unidentified nodes_ page. When its status is _READY_, the node can be categorized via the step 5 described in [Running dEF-Pi on a local Linux machine](running-def-pi-on-linux.md#step-5-categorize-unidentified-nodes)