# Package Chaincode

on the manager mode, connect to the cli container

    docker ps|grep cli
    docker exec -it <container id>  bash

run inside the container
    
    export CC_NAME=basic
    export CHANNEL_NAME=mychannel
    ./scripts/package_cc.sh
    
# Install Chaincode

Copy the packaged chaincode from manager to each worker

    scp /home/ubuntu/hlf-docker-swarm/chaincode/basic.tar.gz ubuntu@worker1:/home/ubuntu/hlf-docker-swarm/chaincode/
    scp /home/ubuntu/hlf-docker-swarm/chaincode/basic.tar.gz ubuntu@worker2:/home/ubuntu/hlf-docker-swarm/chaincode/

On each node inside the cli container run

    ./scripts/install_cc.sh

# Approve Chaincode

On each node inside the cli container run

    ./scripts/approve_cc.sh
    
To check the status, run 

    ./scripts/check_commit.sh
    
which will return an output similar to

    Org1MSP: true
    Org2MSP: false
    Org3MSP: false
    
depending on which organizations have approved the chaincode    

# Commit Chaincode

On each node inside the cli container run

    ./scripts/commit_cc.sh

# Invoke Transactions

On manager node inside the cli container run

    source ./scripts/envVar.sh
    parsePeerConnectionParameters 1 2 3
    ./scripts/invoke_cc.sh
    
Alternatively on node 2, run     
    
    source ./scripts/envVar.sh
    parsePeerConnectionParameters 2
    ./scripts/invoke_cc.sh
