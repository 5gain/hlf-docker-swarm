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

## Ensure Clean Set-Up

Ensure that on each node the following directories are empty, otherwise clean them

    rm -fr organizations/peerOrganizations/*
    rm -fr organizations/ordererOrganizations/*
    rm -fr channel-artifacts
    
Also check that no old volumes are available on each node

    docker volume ls|grep hlf
    
Delete any found volumes

    docker volumne rm <volume name>

## Run on manager node:

    source ./organizations/fabric-ca/registerEnroll.sh
    createOrg1
    createOrderer
    
## Run on worker1

    source ./organizations/fabric-ca/registerEnroll.sh
    createOrg2
    
## Run on worker2

    source ./organizations/fabric-ca/registerEnroll.sh
    createOrg3
    
# Copying Certificates to other nodes

Assuming that aliases for manager, worker1 and worker2 have been set up in ``/etc/hosts`` 

## Copy Orderer Certificates from manager node to workers

    scp -r organizations/ordererOrganizations/example.com ubuntu@worker1:/home/ubuntu/hlf-docker-swarm/test-network/organizations/ordererOrganizations/
    scp -r organizations/ordererOrganizations/example.com ubuntu@worker2:/home/ubuntu/hlf-docker-swarm/test-network/organizations/ordererOrganizations/
 
## Copy Org1 Certificates from manager node to workers

    scp -r organizations/peerOrganizations/org1.example.com ubuntu@worker1:/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations/
    scp -r organizations/peerOrganizations/org1.example.com ubuntu@worker2:/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations/
    
## Copy Org2 Certificates from worker1 node to manager and worker2

    scp -r organizations/peerOrganizations/org2.example.com ubuntu@manager:/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations/
    scp -r organizations/peerOrganizations/org2.example.com ubuntu@worker2:/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations/

## Copy Org3 Certificates from worker2 node to manager and worker1

    scp -r organizations/peerOrganizations/org3.example.com ubuntu@manager:/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations/
    scp -r organizations/peerOrganizations/org3.example.com ubuntu@worker1:/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations/

# Creating genesis blocks, channel transaction, anchor peers

Create the genesis block on the manager node

    ./scripts/createGenesis.sh

Create channel transaction and the anchor peers
    
    ./scripts/createChannelTx.sh
    
# Copy Anchor Peers

# Starting Peers, Orderer and CouchDb

    docker stack deploy -c docker/docker-compose-test-net.yaml -c docker/docker-compose-couch.yaml hlf

# Creating Application Channel

Deploy the Fabric Tools client containers on each node:

    docker stack deploy -c docker/docker-compose-cli.yaml hlf

Get the container ID on the manager node

    docker ps|grep cli
    cc0e0e377563   stefanklinger/fabric-tools:2.4.0     "/bin/bash"              About a minute ago   Up About a minute hlf_cliOrg1.1.t7fuq3x1k56dtysa6q7l0tpyq

An run the bash shell inside the container, using the returned container id

    docker exec -it cc0e0e377563  bash
    
and run the following commands inside the container

    export CHANNEL_NAME=mychannel
    ./scripts/create_app_channel.sh

# Copy Channel Block to Workers


# Joining Channel

Run inside the cli container on each node

   peer channel join -b ./channel-artifacts/mychannel.block


# Update Anchor Peers

Run inside the cli container on the nodes.

Manager:

    ./scripts/updateAnchorPeer.sh mychannel Org1MSP

Worker1:

    ./scripts/updateAnchorPeer.sh mychannel Org2MSP

Worker2:

    ./scripts/updateAnchorPeer.sh mychannel Org3MSP
