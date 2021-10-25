# Package Chaincode

    docker ps|grep cli
    
    docker exec -it f2949637503b  bash
    
    export CC_NAME=basic
    export CHANNEL_NAME=mychannel
    ./scripts/package_cc.sh
    
    ./scripts/install_cc.sh

# Approve Chaincode

On each node

    ./scripts/approve_cc.sh
    
To check the status, run 

    ./scripts/check_commit.sh
    
which will return an output similar to

    Org1MSP: true
    Org2MSP: false
    Org3MSP: false

# Commit Chaincode

On each node

    ./scripts/commit_cc.sh

# Invoke Transactions

On manager node

    source ./scripts/envVar.sh
    parsePeerConnectionParameters 1 2 3
    
    echo $PEER_CONN_PARMS
    ./scripts/invoke_cc.sh
    
