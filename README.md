# Hyperledger Exploration  ![](https://img.shields.io/badge/-Live-darkgreen)
![](https://img.shields.io/badge/Domain-Blockchain-blue) ![](https://img.shields.io/badge/Blockchain-Hyperledger-brown) ![](https://img.shields.io/badge/Hyperledger-Bevel-gold) <br/> ![](https://img.shields.io/badge/Reviewed-None-bronze) <br/> 

## Hyperledger Bevel
![](https://img.shields.io/badge/Exploration_By-Amal_Ritessh_A_P-gold)  <br/>
![](https://img.shields.io/badge/Start-None-silver) ![](https://img.shields.io/badge/End-None-silver) 

 <p align="center"><img src="./Hyperledger_Project Logos_Master_Hyperledger_Bevel.svg" width=320> </p>

### Introduction
Hyperledger Bevel is an advanced automation framework tailored for the 
seamless deployment of robust, production-ready Distributed Ledger 
Technology (DLT) networks on cloud-based infrastructures. Eliminating the 
need for intricate solution architecture, Bevel empowers teams to deliver with 
precision 

### Which platforms does Bevel Support?
Bevel currently supports the following DLT/Blockchain Platforms:
- R3 Corda
- Hyperledger Fabric
- Hyperledger Indy
- Hyperledger Besu
- Quorum
- Substrate

  
## Pre-requisites
- Git Repository
- HashiCorp Vault
- Minikube
- Docker

### Git Repository

- So first you are asked to fork Bevel repo from hyperledger/bevel main branch
- then you have to create a git token
- once its done, follow the commands to clone repo in host machine

``` bash
mkdir project
cd project
git clone https://<user_name>:<git_token>@github.com/<user_name>/bevel.git
git remote set-url origin https://<user_name>:<git_token>@github.com/<user_name>/bevel.git #This command changes the remote repository URL to include a GitHub username and token for authentication.
```

-  to create an local branch in you forked repo follow the commands

``` bash
cd bevel
git checkout develop # to get latest code
git pull
git checkout -b local
git push --set-upstream origin local
```

- to verify, you can check if local branch has been created to you forked bevel repo

### HashiCorp Vault
- We need Hashicorp Vault for the certificate and key storage.
- To install the binary file, use the following command

```bash
wget https://releases.hashicorp.com/vault/1.17.1/vault_1.17.1_linux_amd64.zip
unzip vault_1.17.1_linux_amd64.zip
```

- once the binary `vault` is downloaded, move it `project/bin` folder

```bash
mkdir project/bin
mv vault ./project/bin/
export PATH=./project/bin:$PATH
```
- create an `config.hcl` file in `project/` folder with the following contents

```bash
ui = true
storage "file" {
   path    = "./project/data"
}
listener "tcp" {
   address     = "0.0.0.0:8200"
   tls_disable = 1
}
disable_mlock = true
```

- Start the Vault server by executing the following command. And initialize the Vault by providing your choice of key shares and threshold.  (this will occupy one terminal)

```bash
vault server -config=config.hcl
```

### Minikube

- For development environment, minikube can be used as the Kubernetes cluster on which the DLT network will be deployed.
- To install the binary file, use the following command.

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm64
mv minikube-linux-arm64 minikube  # rename the binary to minikube
```

- Move the binary `minikube`, to path `project/bin/` folder
  
```bash
mv minikube ./project/bin/
```

### Docker
- Install Docker Desktop from their website [Docker Desktop](https://www.docker.com/products/docker-desktop/)

#### Directory Structure (After Pre-requisites)
```bash
project
│   ├── config.hcl
│
├── bin
│   ├── vault
│   └── minikube
│
└── bevel
```
### References
 - [Hyperledger Besu Documentation](https://besu.hyperledger.org/)
