# temp
```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
```
```bash
##############################################################################################
#  Copyright Accenture. All Rights Reserved.
#
#  SPDX-License-Identifier: Apache-2.0
##############################################################################################

---
# yaml-language-server: $schema=../../../../platforms/network-schema.json
# This is a sample configuration file for testing Indy deployment on minikube which has 3 nodes.
network:
  # Network level configuration specifies the attributes required for each organization
  # to join an existing network.
  type: indy
  version: 1.11.0                         # Supported versions 1.11.0 and 1.12.1

  #Environment section for Kubernetes setup
  env:
    type: "bevel"                     # tag for the environment. Important to run multiple flux on single cluster
    proxy: none               # proxy is none for minikube/single cluster
    retry_count: 20                 # Retry count for the checks
    external_dns: disabled          # Should be enabled if using external-dns for automatic route configuration

  # Docker registry details where images are stored. This will be used to create k8s secrets
  # Please ensure all required images are built and stored in this registry.
  # Do not check-in docker_password.
  docker:
    url: "index.docker.io/hyperledgerlabs"
    #username: "docker_username"
    #password: "docker_password"

  # It's used as the Indy network name (has impact e.g. on paths where the Indy nodes look for crypto files on their local filesystem)
  name: bevel

  # Informatio about pool transaction genesis and domain transactions genesis
  genesis:
    state: absent     # must be absent when network is created from scratch
    pool: /home/bevel/build/pool_transactions_genesis       # path where pool_transactions_genesis will be stored locally
    domain: /home/bevel/build/domain_transactions_genesis   # path where domain_transactions_genesis will be stored locally

  # Allows specification of one or many organizations that will be connecting to a network.
  organizations:
    # Specification for the 1st organization. Each organization maps to a VPC and a separate k8s cluster
    - organization:
      name: bevel-authority
      type: peer
      cloud_provider: minikube             
      publicIps: ["192.168.49.2"]                        # Public Ips of stewards/nodes [public ip of minikube]

      # Kubernetes cluster deployment variables. The config file path has to be provided in case
      # the cluster has already been created.
      k8s:
        config_file: "/home/bevel/build/config"
        context: "minikube"

      # Hashicorp Vault server address and root-token. Vault should be unsealed.
      # Do not check-in root_token
      vault:
        url: "http://10.255.255.254:8200"
        root_token: "hvs.Oo5clvzosryyyVXp9tEwplVi"
        
      # Git Repo details which will be used by GitOps/Flux.
      # Do not check-in git_access_token
      gitops:
        git_protocol: "https" # Option for git over https or ssh
        git_url: "https://github.com/adh2004amal/bevel.git"                                            # Gitops https or ssh url for flux value files like "https://github.com:hyperledger/bevel.git"
        branch: "local"                                                      # Git branch where release is being made
        release_dir: "platforms/hyperledger-indy/releases/dev"               # Relative Path in the Git repo for flux sync per environment.
        chart_source: "platforms/hyperledger-indy/charts"                    # Relative Path where the Helm charts are stored in Git repo
        git_repo: "github.com/adh2004amal/bevel.git"                                     # Gitops git repository URL for git push 
        username: "adh2004amal"                                          # Git Service user who has rights to check-in in all branches
        password: "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"          # Git Server user password/token (Optional for ssh; Required for https)
        email: "ashokamal2004@email.com"                                                # Email to use in git config
        private_key: "/home/bevel/build/gitops"    # Path to private key file which has write-access to the git repo (Optional for https; Required for ssh)

      # Services maps to the pods that will be deployed on the k8s cluster
      # This sample has trustee
      services:
        trustees:
        - trustee:
          name: authority-trustee
          genesis: true
          server:
            port: 8000
            ambassador: 15010

    # Specification for the 2nd organization. Each organization maps to a VPC and a separate k8s cluster
    - organization:
      name: bevel-provider
      type: peer
      cloud_provider: minikube             
      publicIps: ["192.168.49.2"]        # Public Ips of stewards/nodes [public ip of minikube]

      # Kubernetes cluster deployment variables. The config file path has to be provided in case
      # the cluster has already been created.
      k8s:
        config_file: "/home/bevel/build/config"
        context: "minikube"

      # Hashicorp Vault server address and root-token. Vault should be unsealed.
      # Do not check-in root_token
      vault:
        url: "http://10.255.255.254:8200"
        root_token: "hvs.Oo5clvzosryyyVXp9tEwplVi"
        
      # Git Repo details which will be used by GitOps/Flux.
      # Do not check-in git_access_token
      gitops:
        git_protocol: "https" # Option for git over https or ssh
        git_url: "https://github.com/adh2004amal/bevel.git"                                             # Gitops https or ssh url for flux value files like "https://github.com:hyperledger/bevel.git"
        branch: "local"                                                      # Git branch where release is being made
        release_dir: "platforms/hyperledger-indy/releases/dev"               # Relative Path in the Git repo for flux sync per environment.
        chart_source: "platforms/hyperledger-indy/charts"                    # Relative Path where the Helm charts are stored in Git repo
        git_repo: "github.com/adh2004amal/bevel.git"                                     # Gitops git repository URL for git push 
        username: "adh2004amal"                                          # Git Service user who has rights to check-in in all branches
        password: "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"          # Git Server user password/token (Optional for ssh; Required for https)
        email: "ashokamal2004@email.com"                                                # Email to use in git config
        private_key: "/home/bevel/build/gitops"    # Path to private key file which has write-access to the git repo (Optional for https; Required for ssh)

      # Services maps to the pods that will be deployed on the k8s cluster
      # This sample has trustee, 2 stewards and endoorser
      services:
        stewards:
        - steward:
          name: provider-steward-1
          type: VALIDATOR
          genesis: true
          publicIp: 192.168.49.2          # [public ip of minikube]
          node:
            port: 15711
            targetPort: 15711
            ambassador: 15711
          client:
            port: 15712
            targetPort: 15712
            ambassador: 15712
        - steward:
          name: provider-steward-2
          type: VALIDATOR
          genesis: true
          publicIp: 192.168.49.2          # [public ip of minikube]
          node:
            port: 15721
            targetPort: 15721
            ambassador: 15721
          client:
            port: 15722
            targetPort: 15722
            ambassador: 15722
        endorsers:
        - endorser:
          name: provider-endorser
          full_name: Some Decentralized Identity Mobile Services Provider
          avatar: http://provider.com/avatar.png
          # public endpoint will be {{ endorser.name}}.{{ external_url_suffix}}:{{endorser.server.httpPort}}
          # Eg. In this sample http://provider-endorser.indy.blockchaincloudpoc.com:15023/
          # For minikube: http://<minikubeip>>:15023
          server:
            httpPort: 15024
            apiPort: 15024
            webhookPort: 15025

    # Specification for the 3rd organization. Each organization maps to a VPC and a separate k8s cluster
    - organization:
      name: bevel-dzamba-partner
      type: peer
      cloud_provider: minikube             
      publicIps: ["192.168.49.2"]        # Public Ips of stewards/nodes [public ip of minikube]

      # Kubernetes cluster deployment variables. The config file path has to be provided in case
      # the cluster has already been created.
      k8s:
        config_file: "/home/bevel/build/config"
        context: "minikube"

      # Hashicorp Vault server address and root-token. Vault should be unsealed.
      # Do not check-in root_token
      vault:
        url: "http://10.255.255.254:8200"
        root_token: "hvs.Oo5clvzosryyyVXp9tEwplVi"
        
      # Git Repo details which will be used by GitOps/Flux.
      # Do not check-in git_access_token
      gitops:
        git_protocol: "https" # Option for git over https or ssh
        git_url: "https://github.com/adh2004amal/bevel.git"                                            # Gitops https or ssh url for flux value files like "https://github.com:hyperledger/bevel.git"
        branch: "local"                                                      # Git branch where release is being made
        release_dir: "platforms/hyperledger-indy/releases/dev"               # Relative Path in the Git repo for flux sync per environment.
        chart_source: "platforms/hyperledger-indy/charts"                    # Relative Path where the Helm charts are stored in Git repo
        git_repo: "github.com/adh2004amal/bevel.git"                                     # Gitops git repository URL for git push 
        username: "adh2004amal"                                          # Git Service user who has rights to check-in in all branches
        password: "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"          # Git Server user password/token (Optional for ssh; Required for https)
        email: "ashokamal2004@email.com"                                                # Email to use in git config
        private_key: "/home/bevel/build/gitops"    # Path to private key file which has write-access to the git repo (Optional for https; Required for ssh)

      # Services maps to the pods that will be deployed on the k8s cluster
      # This sample has trustee, 2 stewards and endoorser
      services:
        stewards:
        - steward:
          name: partner-steward-1
          type: VALIDATOR
          genesis: true
          publicIp: 192.168.49.2          # [public ip of minikube]
          node:
            port: 15731
            targetPort: 15731
            ambassador: 15731
          client:
            port: 15732
            targetPort: 15732
            ambassador: 15732
        - steward:
          name: partner-steward-2
          type: VALIDATOR
          genesis: true
          publicIp: 192.168.49.2          # [public ip of minikube]
          node:
            port: 15741
            targetPort: 15741
            ambassador: 15741
          client:
            port: 15742
            targetPort: 15742
            ambassador: 15742
        endorsers:
        - endorser:
          name: partner-endorser
          full_name: Some Decentralized Identity Mobile Services Partner
          avatar: http://partner.com/avatar.png
          # public endpoint will be {{ endorser.name}}.{{ external_url_suffix}}:{{endorser.server.httpPort}}
          # Eg. In this sample http://provider-endorser.indy.blockchaincloudpoc.com:15033/
          # For minikube: http://<minikubeip>>:15033
          server:
            httpPort: 15033
            apiPort: 15034
            webhookPort: 15035

```

```bash
##############################################################################################
#  Copyright Accenture. All Rights Reserved.
#
#  SPDX-License-Identifier: Apache-2.0
##############################################################################################

---
# yaml-language-server: $schema=../../../../platforms/network-schema.json
# This is a sample configuration file for testing Fabric deployment which has 3 nodes.
network:
  # Network level configuration specifies the attributes required for each organization
  # to join an existing network.
  type: fabric
  version: 2.2.2                 # currently tested 1.4.4, 1.4.8 and 2.2.2

  #Environment section for Kubernetes setup
  env:
    type: "local"             # tag for the environment. Important to run multiple flux on single cluster
    proxy: none               # 'none' can be used in single-cluster networks to don´t use a proxy
    retry_count: 50                 # Retry count for the checks
    external_dns: disabled          # Should be enabled if using external-dns for automatic route configuration
    annotations:              # Additional annotations that can be used for some pods (ca, ca-tools, orderer and peer nodes)
      service: 
       - example1: example2
      deployment: {} 
      pvc: {}

  # Docker registry details where images are stored. This will be used to create k8s secrets
  # Please ensure all required images are built and stored in this registry.
  # Do not check-in docker_password.
  docker:
    url: "ghcr.io/hyperledger"
    #username: "docker_username"
    #password: "docker_password"

  # Remote connection information for orderer (will be blank or removed for orderer hosting organization)
  # For RAFT consensus, have odd number (2n+1) of orderers for consensus agreement to have a majority.
  consensus:
    name: raft
  orderers:
    - orderer:
      type: orderer
      name: orderer1
      org_name: supplychain                 # org_name should match one organization definition below in organizations: key            
      uri: orderer1.supplychain-net:7050    # Internal URI for orderer which should be reachable by all peers
      certificate: /home/bevel/build/orderer1.crt   # the directory should be writable

  # The channels defined for a network with participating peers in each channel
  channels:
  - channel:
    consortium: SupplyChainConsortium
    channel_name: AllChannel
    chaincodes:
      - "chaincode_name"
    orderers:
      - supplychain
    participants:
    - organization:
      name: carrier
      type: creator       # creator organization will create the channel and instantiate chaincode, in addition to joining the channel and install chaincode
      org_status: new
      peers:
      - peer:
        name: peer0
        gossipAddress: peer0.carrier-net:7051         # Internal URI of the gossip peer
        peerAddress: peer0.carrier-net:7051           # Internal URI of the peer
      ordererAddress: orderer1.supplychain-net:7050   # Internal URI of the orderer
    - organization:
      name: manufacturer
      type: joiner
      org_status: new
      peers:
      - peer:
        name: peer0
        gossipAddress: peer0.manufacturer-net:7051
        peerAddress: peer0.manufacturer-net:7051      # Internal URI of the peer
      ordererAddress: orderer1.supplychain-net:7050
    endorsers:
    # Only one peer per org required for endorsement
    - organization:
      name: carrier
      peers:
      - peer:
        name: peer0
        corepeerAddress: peer0.carrier-net:7051
        certificate: "/home/bevel/build/carrier/server.crt" # certificate path for peer
    - organization:
      name: manufacturer
      peers:
      - peer:
        name: peer0
        corepeerAddress: peer0.manufacturer-net:7051
        certificate: "/home/bevel/build/manufacturer/server.crt" # certificate path for peer
    genesis:
      name: OrdererGenesis

  # Allows specification of one or many organizations that will be connecting to a network.
  # If an organization is also hosting the root of the network (e.g. doorman, membership service, etc),
  # then these services should be listed in this section as well.
  organizations:

    # Specification for the 1st organization. Each organization maps to a VPC and a separate k8s cluster
    - organization:
      name: supplychain
      country: UK
      state: London
      location: London
      subject: "O=Orderer,L=51.50/-0.13/London,C=GB"
      type: orderer
      external_url_suffix: develop.local.com   # Ignore for proxy none
      org_status: new
      fabric_console: enabled
      ca_data:
        url: ca.supplychain-net:7054
        certificate: /home/bevel/build/supplychain/server.crt

      cloud_provider: minikube   # Options: aws, azure, gcp
      aws:
        access_key: "aws_access_key"        # AWS Access key, only used when cloud_provider=aws
        secret_key: "aws_secret_key"        # AWS Secret key, only used when cloud_provider=aws

      # Kubernetes cluster deployment variables. The config file path and name has to be provided in case
      # the cluster has already been created.
      k8s:
        region: "minikube"
        context: "minikube"
        config_file: "/home/bevel/build/config"

      # Hashicorp Vault server address and root-token. Vault should be unsealed.
      # Do not check-in root_token
      vault:
        url: "http://10.255.255.254:8200" #have2
        root_token: "hvs.Oo5clvzosryyyVXp9tEwplVi"
        secret_path: "secretsv2"

      # Git Repo details which will be used by GitOps/Flux.
      # Do not check-in git_access_token
      gitops:
        git_protocol: "https" # Option for git over https or ssh
        git_url: "https://github.com/adh2004amal/bevel.git"  # Gitops https or ssh url for flux value files 
        branch: "local"                                          # Git branch where release is being made
        release_dir: "platforms/hyperledger-fabric/releases/dev" # Relative Path in the Git repo for flux sync per environment.
        chart_source: "platforms/hyperledger-fabric/charts"      # Relative Path where the Helm charts are stored in Git repo
        git_repo: "github.com/adh2004amal/bevel.git"              # Gitops git repository URL for git push  (without https://)
        username: "adh2004amal"                              # Git user who has rights to check-in in all branches
        password: "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"                             # Git Server user password/token (Optional for ssh; Required for https)
        email: "ashokamal2004@email.com"                                   # Email to use in git config
        private_key: "/home/bevel/build/gitops"                  # Path to private key file which has write-access to the git repo (Optional for https; Required for ssh)

      # Services maps to the pods that will be deployed on the k8s cluster
      # This sample is an orderer service and includes a raft consensus
      services:
        ca:
          name: ca
          subject: "/C=GB/ST=London/L=London/O=Orderer/CN=ca.supplychain-net"
          type: ca
          grpc:
            port: 7054

        consensus:
          name: raft
        
        orderers:
        - orderer:
          name: orderer1
          type: orderer
          consensus: raft
          grpc:
            port: 7050
          metrics:
            enabled: false
            port: 9443
          ordererAddress: orderer1.supplychain-net:7050

    # Specification for the 2nd organization. Each organization maps to a VPC and a separate k8s cluster
    - organization:
      name: manufacturer
      country: CH
      state: Zurich
      location: Zurich
      subject: "O=Manufacturer,OU=Manufacturer,L=47.38/8.54/Zurich,C=CH"
      type: peer
      external_url_suffix: develop.local.com   # Ignore for proxy none
      org_status: new
      orderer_org: supplychain # Name of the organization that provides the ordering service
      ca_data:
        url: ca.manufacturer-net:7054
        certificate: /home/bevel/build/manufacturer/server.crt

      cloud_provider: minikube   # Options: aws, azure, gcp
      aws:
        access_key: "aws_access_key"        # AWS Access key, only used when cloud_provider=aws
        secret_key: "aws_secret_key"        # AWS Secret key, only used when cloud_provider=aws

      # Kubernetes cluster deployment variables. The config file path and name has to be provided in case
      # the cluster has already been created.
      k8s:
        region: "minikube"
        context: "minikube"
        config_file: "/home/bevel/build/config"

      # Hashicorp Vault server address and root-token. Vault should be unsealed.
      # Do not check-in root_token
      vault:
        url: "http://10.255.255.254:8200" #have2
        root_token: "hvs.Oo5clvzosryyyVXp9tEwplVi"
        secret_path: "secretsv2"

      # Git Repo details which will be used by GitOps/Flux.
      # Do not check-in git_access_token
      gitops:
        git_protocol: "https" # Option for git over https or ssh
        git_url: "https://github.com/adh2004amal/bevel.git"  # Gitops https or ssh url for flux value files 
        branch: "local"                                          # Git branch where release is being made
        release_dir: "platforms/hyperledger-fabric/releases/dev" # Relative Path in the Git repo for flux sync per environment.
        chart_source: "platforms/hyperledger-fabric/charts"      # Relative Path where the Helm charts are stored in Git repo
        git_repo: "github.com/adh2004amal/bevel.git"              # Gitops git repository URL for git push  (without https://)
        username: "adh2004amal"                              # Git user who has rights to check-in in all branches
        password: "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"                             # Git Server user password/token (Optional for ssh; Required for https)
        email: "ashokamal2004@email.com"                                   # Email to use in git config
        private_key: "/home/bevel/build/gitops"                  # Path to private key file which has write-access to the git repo (Optional for https; Required for ssh)

      # Generating User Certificates with custom attributes using Fabric CA in BAF for Peer Organizations
      users:
      - user:
        identity: user1
        attributes:
          - key: "hf.Revoker"
            value: "true"
      # The participating nodes are peers
      # This organization hosts it's own CA server
      services:
        ca:
          name: ca
          subject: "/C=CH/ST=Zurich/L=Zurich/O=Manufacturer/CN=ca.manufacturer-net"
          type: ca
          grpc:
            port: 7054
        peers:
        - peer:
          name: peer0          
          type: anchor                                          # This can be anchor/nonanchor. Atleast one peer should be anchor peer.         
          gossippeeraddress: peer0.manufacturer-net:7051        # Internal Address of the other peer in same Org for gossip, same peer if there is only one peer  
          peerAddress: peer0.manufacturer-net:7051              # Internal URI of the peer
          certificate: /home/bevel/build/manufacturer/peer0.crt # Path to peer Certificate
          cli: enabled                                          # Creates a peer cli pod depending upon the (enabled/disabled) tag.
          cactus_connector: enabled                             # set to enabled to create a cactus connector for Fabric
          grpc:
            port: 7051
          events:
            port: 7053
          couchdb:
            port: 5984
          restserver:           # This is for the rest-api server
            targetPort: 20001
            port: 20001
          expressapi:           # This is for the express api server
            targetPort: 3000
            port: 3000
          chaincodes:
            - name: "supplychain"    # This has to be replaced with the name of the chaincode
              version: "1"           # This has to be replaced with the version of the chaincode (do NOT use .)
              maindirectory: "cmd"   # The main directory where chaincode is needed to be placed
              lang: "golang"  # The language in which the chaincode is written ( golang/java/node )
              repository:
                username: "adh2004amal"          # Git user with read rights to repo
                password: "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"         # Git token of above user
                url: "github.com/adh2004amal/bevel-samples.git"
                branch: main
                path: "examples/supplychain-app/fabric/chaincode_rest_server/chaincode/"  #The path to the chaincode in the repo
              arguments: '\"init\",\"\"'                                                  #Arguments to be passed along with the chaincode parameters
              endorsements: ""                                                            #Endorsements (if any) provided along with the chaincode
          metrics:
            enabled: false
            port: 9443
    - organization:
      name: carrier
      country: GB
      state: London
      location: London
      subject: "O=Carrier,OU=Carrier,L=51.50/-0.13/London,C=GB"
      type: peer
      external_url_suffix: develop.local.com   # Ignore for proxy none
      org_status: new
      orderer_org: supplychain # Name of the organization that provides the ordering service
      ca_data:
        url: ca.carrier-net:7054
        certificate: /home/bevel/build/carrier/server.crt

      cloud_provider: minikube   # Options: aws, azure, gcp
      aws:
        access_key: "aws_access_key"        # AWS Access key, only used when cloud_provider=aws
        secret_key: "aws_secret_key"        # AWS Secret key, only used when cloud_provider=aws

      # Kubernetes cluster deployment variables. The config file path and name has to be provided in case
      # the cluster has already been created.
      k8s:
        region: "minikube"
        context: "minikube"
        config_file: "/home/bevel/build/config"

      # Hashicorp Vault server address and root-token. Vault should be unsealed.
      # Do not check-in root_token
      vault:
        url: "http://10.255.255.254:8200" #have2
        root_token: "hvs.Oo5clvzosryyyVXp9tEwplVi"
        secret_path: "secretsv2"

      # Git Repo details which will be used by GitOps/Flux.
      # Do not check-in git_access_token
      gitops:
        git_protocol: "https" # Option for git over https or ssh
        git_url: "https://github.com/adh2004amal/bevel.git"  # Gitops https or ssh url for flux value files 
        branch: "local"                                          # Git branch where release is being made
        release_dir: "platforms/hyperledger-fabric/releases/dev" # Relative Path in the Git repo for flux sync per environment.
        chart_source: "platforms/hyperledger-fabric/charts"      # Relative Path where the Helm charts are stored in Git repo
        git_repo: "github.com/adh2004amal/bevel.git"              # Gitops git repository URL for git push  (without https://)
        username: "adh2004amal"                              # Git user who has rights to check-in in all branches
        password: "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"                             # Git Server user password/token (Optional for ssh; Required for https)
        email: "ashokamal2004@email.com"                                   # Email to use in git config
        private_key: "/home/bevel/build/gitops"                  # Path to private key file which has write-access to the git repo (Optional for https; Required for ssh)

      # Generating User Certificates with custom attributes using Fabric CA in BAF for Peer Organizations
      users:
      - user:
        identity: user1
        attributes:
          - key: "hf.Revoker"
            value: "true"
      services:
        ca:
          name: ca
          subject: "/C=GB/ST=London/L=London/O=Carrier/CN=ca.carrier-net"
          type: ca
          grpc:
            port: 7054
        peers:
        - peer:
          name: peer0          
          type: anchor                                      # This can be anchor/nonanchor. Atleast one peer should be anchor peer.    
          gossippeeraddress: peer0.carrier-net:7051         # Internal Address of the other peer in same Org for gossip, same peer if there is only one peer  
          peerAddress: peer0.carrier-net:7051               # Internal URI of the peer
          certificate: /home/bevel/build/carrier/peer0.crt  # Path to peer Certificate
          cli: disabled                                     # Creates a peer cli pod depending upon the (enabled/disabled) tag.
          cactus_connector: disabled                        # set to enabled to create a cactus connector for Fabric
          grpc:
            port: 7051
          events:
            port: 7053
          couchdb:
            port: 5984
          restserver:
            targetPort: 20001
            port: 20001
          expressapi:
            targetPort: 3000
            port: 3000
          chaincodes:
            - name: "supplychain"    # This has to be replaced with the name of the chaincode
              version: "1"           # This has to be replaced with the version of the chaincode (do NOT use .)
              maindirectory: "cmd"   # The main directory where chaincode is needed to be placed
              lang: "golang"  # The language in which the chaincode is written ( golang/java/node )
              repository:
                username: "adh2004amal"          # Git user with read rights to repo
                password: "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"         # Git token of above user
                url: "github.com/adh2004amal/bevel-samples.git"
                branch: main
                path: "examples/supplychain-app/fabric/chaincode_rest_server/chaincode/"  #The path to the chaincode in the repo
              arguments: '\"init\",\"\"'                                                  #Arguments to be passed along with the chaincode parameters
              endorsements: ""                                                            #Endorsements (if any) provided along with the chaincode

```
