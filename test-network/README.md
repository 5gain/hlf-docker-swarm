# Fabric CA Setup

The Fabric CA Docker images (1.4.8) for ARM64 were created based on this description: [https://github.com/chinyati/hyperledger-fabric-arm64-images]

## Create Network

Execute the following command on the manager node:

    docker network create --driver=overlay --attachable test 
    docker network ls
    
The output will look something like this:

    NETWORK ID     NAME                      DRIVER    SCOPE
    1658d5b9bcb3   bridge                    bridge    local
    18591e831c38   docker_gwbridge           bridge    local
    7aaf6afa293e   host                      host      local
    t156j5hfnlu9   ingress                   overlay   swarm
    f301eac52804   none                      null      local
    z1ii6q1m38v6   portainer_agent_network   overlay   swarm
    ppkhb513role   test                      overlay   swarm
    
## Label Nodes

Determine the Nodes IDs in the network. The hostnames were previously set in ``/etc/hostname``

    docker node ls

    ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
    vhy90taorh248okmw9jjt932a *   manager    Ready     Active         Leader           20.10.10-rc1
    jq5jxulomz2jhl3hu26c0sfto     worker1    Ready     Active                          20.10.9
    2da4qs5bu48ml0k0uqvt4xfos     worker2    Ready     Active                          20.10.9

Execute the following commands, replace the IDs accordingly:

    docker node update --label-add name=manager vhy90taorh248okmw9jjt932a
    docker node update --label-add name=worker1 jq5jxulomz2jhl3hu26c0sfto
    docker node update --label-add name=worker2 2da4qs5bu48ml0k0uqvt4xfos

## Deploy CA Services

    docker stack deploy -c docker/docker-compose-ca.yaml hlf
    
# Generating Certificates for Peers and Orderers

Run on manager node:

    source ./organizations/fabric-ca/registerEnroll.sh
    createOrg1
    createOrderer
    
Run on worker1

    source ./organizations/fabric-ca/registerEnroll.sh
    createOrg2
    
Run on worker2

    source ./organizations/fabric-ca/registerEnroll.sh
    createOrg3
