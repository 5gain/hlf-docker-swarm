# Build docker images

    docker build -t stefanklinger/caliper:1.0 .

# Update Links to certificates

# Verify connection parameters    

# Run Caliper

    npx caliper launch manager --caliper-workspace ./ --caliper-networkconfig networks/networkConfig.yaml --caliper-benchconfig benchmarks/myAssetBenchmark.yaml --caliper-flow-only-test --caliper-fabric-gateway-enabled
