# Build docker images

The caliper docker image was previously created by running:

    docker build -t stefanklinger/caliper:1.0 .

# Update Links to certificates

In the [network config](networks/networkConfig.yaml) file, edit and change the name of the client private key files for each organisation:

    clientPrivateKey:
          path: '../hlf-docker-swarm/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/keystore/6175b5029b90960a317434108c3b25c2a1cb9d3dff203c4c8761d3887e7251d1_sk'

# Verify connection parameters    

# Run Caliper

    npx caliper launch manager --caliper-workspace ./ --caliper-networkconfig networks/networkConfig.yaml --caliper-benchconfig benchmarks/myAssetBenchmark.yaml --caliper-flow-only-test --caliper-fabric-gateway-enabled
