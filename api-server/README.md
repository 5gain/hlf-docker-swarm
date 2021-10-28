## Create Connection Profile

The API Server requires the connection profiles to connect to each organization anchor peer

    /home/ubuntu/hlf-docker-swarm/test-network$ source ./organizations/ccp-generate.sh
    /home/ubuntu/hlf-docker-swarm/test-network$ Org1CCP
    /home/ubuntu/hlf-docker-swarm/test-network$ Org2CCP
    /home/ubuntu/hlf-docker-swarm/test-network$ Org3CCP
    
The generated connection profiles need to be copied to the api server directory    

## Containerizing API Server

    /home/ubuntu/hlf-docker-swarm/api-server$ docker build -t stefanklinger/node-app:1.0 .
    
    /home/ubuntu/hlf-docker-swarm/api-server$ docker stack deploy -c stack.yaml hlf

## Invoking Transactions
