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

