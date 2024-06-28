# Hyperledger Exploration  ![](https://img.shields.io/badge/-Live-darkgreen)
![](https://img.shields.io/badge/Domain-Blockchain-blue) ![](https://img.shields.io/badge/Blockchain-Hyperledger-brown) ![](https://img.shields.io/badge/Hyperledger-Bevel-gold) <br/> ![](https://img.shields.io/badge/Reviewed-None-bronze) <br/> 

## Hyperledger Bevel
![](https://img.shields.io/badge/Exploration_By-Amal_Ritessh_A_P-gold)  <br/>
![](https://img.shields.io/badge/Start-None-silver) ![](https://img.shields.io/badge/End-None-silver) 

 <p align="center"><img src="../../logos/Hyperledger_Besu.jpg" width=320> </p>

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
- Minikube
- HashiCorp Vault
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

### Minikube


##### System Tools #####
```
sudo apt install wget curl
```

After verifying and installing the prerequisites we can proceed with the step by step installation process

#### Besu Installation and Configuration
##### Step 1: Download Hyperledger Besu
- This command uses wget to download the specified version of Hyperledger Besu from its official GitHub releases page. 
```
 wget https://github.com/hyperledger/besu/releases/download/24.5.2/besu-24.5.2.tar.gz
 ```
- Extracting the tar file
 ```
 tar -xzf besu-24.5.2.tar.gz  
 ``` 
##### Step 2: Setting the path variable
- Navigates into the directory where Besu was extracted.
 ```
cd besu-24.5.2  
 ```
- This command adds the current directory's bin subdirectory to the system PATH using $(pwd) to dynamically insert the current directory path.
 ```
export PATH=$PATH:$(pwd)/bin
 ```
##### Step 3: Setting up the ~./bashrc file
- The echo command appends the PATH export statement to the ~/.bashrc file, ensuring the PATH change is applied in future terminal sessions.
 ```
echo 'export PATH=$PATH:$(pwd)/bin' >> ~/.bashrc  
 ```
- The source ~/.bashrc command reloads the ~/.bashrc file to apply the PATH changes immediately.
 ```
echo 'export PATH=$PATH:$(pwd)/bin' >> ~/.bashrc  
 ```
##### Step 4: Verifing the installantion
- Running besu --version verifies that Besu is installed correctly and accessible in your terminal. 
 ```
besu –version 
 ```

### Running the Network

### Deploying the Smart Contracts

### References
 - [Hyperledger Besu Documentation](https://besu.hyperledger.org/)
