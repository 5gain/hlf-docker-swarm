# Hyperledger Fabric with Docker Swarm 

This is a setup of a Hyperledger Fabric test network with 3 nodes an ARM64 architecture. At the moment, HLF does not support ARM64 out of the box. 

The Fabric images und binaries were created using the fork vom main branch (2.4.0) here (https://github.com/5gain/fabric)

## Creating a swarm network

To set up a Docker Swarm Network, simply follow the instructions (https://dockerswarm.rocks/)

## Setup the HLF network

See section on [Setting up the test network](test-network/README.md)

## Deploy Chaincode

See section on [Deploying the Chaincode](chaincode/README.md)

## Create API Server


## Run Caliper

See section on [Caliper](caliper-workspace/README.md)

## Lessons Learned

The most problems occurred when the network was not created from a clean state. Thus ensure that no old volumes, certificates or transactions are left behind before re-creating the network.

Another problem is an inconsistency between the version of the docker containers. If you make changes to containers, ensure that this version is also propagated to the other nodes (e.g. using ``docker pull`` or ``docker save <image> | bzip2 | ssh user@host docker load``).

The standard Fabric Images are based on Alpine Linux. Unfortunately, the peer image produced segmentation faults when starting. The GDB Debug showed a problem with musl. This Problem does not occurr with Ubuntu, as this distribution uses standard libc.

Another issue occurred with the version of the gRPC und Protobuf libraries, the dependencies in the main branch were pretty old. These libraries only recently received official support for the ARM64 architecture: https://grpc.io/blog/grpc-on-arm64/. Thus an update to newer versions eliminated the observed errors.
