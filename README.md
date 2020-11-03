# DeFi Simulation System 🎛️

- purpose: to abstract the complexity of scaling/growing infrastructure and serve as unified environment for testing and simulation of new products/strategies on DeFi.
- self-contained, portable, fully configurable and expandable system based on Moncc configuration and orchestration.
- deploy to local, dev or production in less than 1 hour.

- 🧪 WIP - automatic Chainlink oracle feeds and adapters components as well as MakerDAO V2, Uniswap V2, dYdX, Compound oracle systems.
- 🧪 WIP - automatic user / LP wallet behaviours components.
- 🧪 WIP - extend to Substrate, RSK and any other network.
- 🧪 WIP - Grafana, Prometheus universal components

Find out more about Moncc: https://docs.moncc.io

Join Moncc Community: https://discord.gg/2YGryc5

Quick showcase of the below stack: https://youtu.be/RV5oj4H0ZB0

## Info

- /ganache - configuration based on official Docker image `trufflesuite/ganache-cli:latest`
- /chainlink - configuration based on official Docker image and documentation. Also contains standalone Chainlink node cluster wtih Cloud LB.
- /interfaces - example based on https://www.trufflesuite.com/boxes/drizzle-truffle-ganache-docker-box
- /poster - example based on Compound Open Oracle https://github.com/compound-finance/open-oracle/tree/master/poster
- /reporter - example based on Compound Open Oracle https://github.com/compound-finance/open-oracle/tree/master/sdk/javascript

## Templates

Moncc component configuration manifests.
```
ganache
└── ganache.yaml
chainlink
└── chainlink.yaml
└── chainlink_cluster.yaml
interfaces
└── interfaces.yaml
└── Dockerfile
poster
└── poster.yaml
└── Dockerfile
reporter
└── reporter.yaml
└── Dockerfile
stack.yaml
```
Each of the components is published in Moncc Hub (https://hub.moncc.io/) or can be loaded locally from the repository.

Moncc Hub Template repository: https://github.com/oaknode/templates

You can browse Moncc Hub via CLI:
```
» mncc list ethereum
✔ Got the list
Type      Template                   Repository  Version  Tags        
runnable  ethereum/latest            moncc       v1.9.13  blockchain  
runnable  ethereum/mainnet-fast      moncc       v1.9.13  blockchain  
runnable  ethereum/mainnet-full      moncc       v1.9.13  blockchain  
runnable  ethereum/mainnet-light     moncc       v1.9.13  blockchain  
runnable  ethereum/parity            moncc       v2.5.9   blockchain 
```

## Start 

Register account at https://moncc.io/join

Get Moncc - https://docs.moncc.io/getting-started/moncc-in-10-minutes

Start `monccd -d` and login to Moncc. `$MONCC_USERNAME` and `$MONCC_PASS` are your credentials from registration above.
```
» mncc login --email=$MONCC_USERNAME --password=$MONCC_PASS
```

## Create a cluster

Create new cluster 
- $CLUSTER_NAME - is your internal name for the cluster
```
» mncc cluster new --name=$CLUSTER_NAME
```

Prepare cloud credentials:
- GCP service account key `/usr/local/etc/key.json` or other directory with full Compute access role.
```
» mncc cluster provider add -p gcp -f /usr/local/etc/key.json
```
- AWS IAM role with full EC2 access role and save your secret keys under `~/.aws/credentials` - Moncc will automatically source from `[default]`
```
[default]
aws_access_key_id=<aws_access_key_id>
aws_secret_access_key=<aws_secret_access_key>

» mncc cluster provider add -p aws
```

Provision cluster node instances
- $PEER_TAG - tag name for instances in the clusters. It's used to point your deployments to selected tags.
```
» mncc cluster grow --provider=gcp --name=n2-gcp --tag=$PEER_TAG --instance-type=n2-standard-2 --region=europe-west4 --disk-size=10 -m 2
```

If you are using CI pipeline i.e Gitlab, you will need to save Monccode for Gitlab runner to connect to the cluster. It will be shown at the bottom of `mncc cluster info` output.
```
» mncc cluster info
✔ Got the list of peers
✔ Got cluster info
Node ID: QmecNCwVApSKXaGeDjmx92ZWPEhyTrZ9RECWMRcFFn9J9g

Token: 9496aeeb-1deb-5237-b07e-d0a15a7962e7

Node addresses: /ip4/...

Monccode: H4sIAAAAAAAA/6zU0XKTQBQG4Bdylj0HCOylCRCHWhA2AcpdA5FiJxRRpHX68I4pOtrkJtP/BT7O2f/wJ4d9Fa2m7H2vr4rb9d77cnhUXOaf/LunzVCq1F/l12kVBJ0KVfMclGGsQ3+bRx/jLNsG+jpOyizdFEv/wzLP42ej7S2DHBZsCynI+F71hmVJSUbPvXHZ1979xUghMQeImRKJERBjF4khA2ALiSHTJOibQdNkJAY7WsWCFq4gQRI120tvjPUf7OvYVojyQIoOWpxrBCkSWpwLBSnCk5mrBSnCsyb8O+KzZriIvfB/iwdkquPOri2IFy+mw2wDzWNBHk0c96a+Pd0YkIwUtPj9F/LpgK2uDrdjpCcv2t+0q8JMdmYWJrWu+/BpSjNvuBrqZqR2Gk6w8+NdLJq2YGkJYhIOYsL/PdCQ89pkC2bzPLrrP4fhg1t36+hn82Nzs+wKbz3d+Y+tXpRd+y1r9P2D2u7s8Z7Ooa83v9wzLaGkIOkIkgruvXnpXwEAAP//UzIr3NkMAAA=
```

Add/Update CICD variable in Gitlab - CI_CLUSTER_MONCCODE to Monccode `H4sIAAAAAAAA/6zU0...NkMAAA=` value above.

### Deploy

Load the template
```
» mncc load stack.yaml 
```

You will need to additionally login via `mncc docker-login` if using private container registry.

Login to Gitlab container registry
- $GITLAB_USERNAME - your Gitlab username 
- $GITLAB_ACCESS_TOKEN - use existing access token, or create a new one under Account Settings/Access Token.
```
» mncc docker-login -s registry.gitlab.com --username=$GITLAB_USERNAME --password=$GITLAB_ACCESS_TOKEN
✔ Successfully authenticated to registry.gitlab.com
```

Deploy the stack
```
» mncc run defi/stack
✔ Starting the job... DONE
✔ Preparing nodes DONE
✔ Preparing containers DONE
✔ New container 98abc9867ba39c798b86819d5c4cb0d1b6789b5ff4a517e89eb29510ec845dd3 created DONE
✔ Checking/pulling images DONE
✔ New container 44fde5a7bb55e38151d1a7df08a5491d291a75e570f481565202ae4d0adf4934 created DONE
✔ New container cbff1fa5eeeff2eb79c1c0319dbe7914402fce1b0def47ef9ad9dce3b6b0660e created DONE
✔ New container 4ad1ccf2517e106da60a338b4b21fcaed2dc858c1289e865069357e5342a5a42 created DONE
✔ Starting containers DONE
✔ New container 52ad90c984a7a2b1fc5d94aada179a7cd37a3312e89a1cc847213c36d6e3356c created DONE
✔ Started defi/stack

✨ All done! 

🔩 defi/stack
 └─🧊 Peer QmSGLfRsidRji89HBVKUqrCBkev4mjPgRRbUsdYy19qYXa
    ├─📦 templates-local-defi-interface-1-interface
    │  ├─🧩 registry.gitlab.com/maciej22/ethereum-fork/interface:master
    │  └─🔌 open localhost:3000 (0.0.0.0:3000) -> 3000
    ├─📦 templates-local-defi-price-source-1-reporter
    │  ├─🧩 registry.gitlab.com/maciej22/ethereum-fork/reporter:master
    │  └─🔌 open localhost:4040 (0.0.0.0:4040) -> 4040
    ├─📦 templates-local-defi-ganache-cli-ganache-cli
    │  ├─🧩 trufflesuite/ganache-cli:latest
    │  └─🔌 open localhost:8545 (0.0.0.0:8545) -> 8545
    ├─📦 templates-local-defi-chainlink-1-chainlink-node
    │  ├─🧩 smartcontract/chainlink:latest
    │  ├─💾 /home/foreachtable/.moncc/volumes/chainlink -> /chainlink
    │  └─🔌 open localhost:6688 (0.0.0.0:6688) -> 6688
    ├─📦 templates-local-defi-price-feed-1-poster
    │  └─🧩 registry.gitlab.com/maciej22/ethereum-fork/poster:master
    └─📦 templates-local-defi-chainlink-db-postgres
       ├─🧩 docker.io/postgres:12-alpine
       ├─💾 /home/foreachtable/.moncc/volumes/db_data -> /var/lib/postgresql/data
       └─🔌 open localhost:5432 (0.0.0.0:5432) -> 5432

💡 You can inspect and manage your above stack with these commands:
	mncc logs (-f) defi/stack - Inspect logs
	mncc shell     defi/stack - Connect to the container's shell
	mncc do        defi/stack/action_name - Run defined action (if exists)
💡 Check mncc help for more!
```

### Update

If you are running the stack locally or are connected to the cluster you can update each component or the whole stack.

Firstly re-load the updated yaml files with `mncc load` then execute `mncc update`:

```
individual component
» mncc update defi/ganache-cli

whole system
» mncc update defi/stack
```

If you have set up CICD pipeline commit and push changes to master - `moncc_deploy` Gitlab step will update the cluster as above.

### Interact

- To test the web app firstly in Metamask create Custom RPC connecting to deployed Ganache network i.e 
```
    ├─📦 templates-local-defi-ganache-cli-ganache-cli
    │  ├─🧩 trufflesuite/ganache-cli:latest
    │  └─🔌 open localhost:8545 (0.0.0.0:8545) -> 8545
```
- Switch Metamask Ethereum wallet to the below
```
Available Accounts
==================
(0) 0x95eDA452256C1190947f9ba1fD19422f0120858a (100 ETH)

Private Keys
==================
(0) 0x31c354f57fc542eba2c56699286723e94f7bd02a4891a0a7f68566c2a2df6795
```
- Open web app in the browser i.e
```
    ├─📦 templates-local-defi-interface-1-interface
    │  ├─🧩 registry.gitlab.com/maciej22/ethereum-fork/interface:master
    │  └─🔌 open localhost:3000 (0.0.0.0:3000) -> 3000
```

### Moncc Actions

- To trigger Open Price Feed poster you can execute Moncc Action below
```
» mncc do defi/price-feed-1/post-prices
```
- To see available actions
```
» mncc list-actions defi/<name of component>
```

### Ethereum Archive Node

Currently the stack is using Infura API to create the forked mainnet and it will stop working after 128 blocks (30minutes).

You can provide the path to your own Ethereum archive node and also you can launch one with Moncc from the Hub:
```
» mncc run ethereum/mainnet-full
✔ Starting the job... DONE
✔ Preparing nodes DONE
✔ Preparing containers DONE
✔ Checking/pulling images DONE
✔ New container 40275cd971d13ee3b6e1126b210faf020b8daa6129e4d5603504a583c56d4e65 created DONE
✔ Started ethereum/mainnet-full

✨ All done!

🔩 ethereum/mainnet-full
 └─🧊 Peer QmT3PMiWrk95DK2b3G6bHa9sKLeWKuP22h6a7h4bRwNxkz
    └─📦 templates-moncc-ethereum-mainnet-full-ethereum-node
       ├─🧩 docker.io/ethereum/client-go:v1.9.13
       ├─💾 /Users/toymachine/.moncc/volumes/ethereum -> /root/.ethereum
       ├─🔌 open udp localhost:30303 (0.0.0.0:30303) -> 30303
       └─🔌 open tcp localhost:8545 (0.0.0.0:8545) -> 8545
```
