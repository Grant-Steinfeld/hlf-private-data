# HYPERLEDGER FABRIC(HLF) FOR THE ENTERPRISE
## Privacy and Security Strategies 

## What you will learn
1. Security with respect to hardware.  Ranging from commodity hardware, to Mainframe running LinuxONE
1. Security quality of HLF ( Audit - Quintessance v1.1 and upcoming audits for v2.0)
1. Privacy options in HLF 
1. How to create and deploy HLF Private Data Collections
1. How to add attributes to your certificates in order to allow for roles and permissions  at the application / client level.

## Presentation Slide decks
This event has been given *4* times so far, and with the feedback and great conversations, the iterations of the talk has continually improved, thanks to all audiences and co-workers for their feedback and collaboration to improve it.  If you have any comments please let us know on twitter @gsteinfeld or open and issue on this repo.  Thanks!

### Best to view the latest!
[Presentation slide deck 08/27/2019](https://github.com/Grant-Steinfeld/hlf-private-data/raw/master/PRESENTATON-Hyperledger-Fabric-for-the-Enterprise-Privacy-and-Security-Strategies-v3.0.2-2019-8-27.pptx) - [NYC Blockhain  Center ](https://blockchaincenter.nyc/)   

`Presenter: Grant Steinfeld`

### Past:
[Presentation given 08/26/2019 deck](https://github.com/Grant-Steinfeld/hlf-private-data/raw/master/PRESENTATON-Hyperledger-Fabric-for-the-Enterprise-Privacy-and-Security-Strategies-v3.0.0-2019-8-26.pptx) - [NYC Blockhains Devs Meetup - WeWork Dumbo ](https://www.meetup.com/nyc-blockchain-devs/)   

`Presenter: Grant Steinfeld`

[Meetup given 06/24/2-19 Presentation deck](https://github.com/Grant-Steinfeld/hlf-private-data/blob/master/Hyperledger-Fabric-for-the-Enterprise-Privacy-and-Security-Strategies-v1.0.0-2019-6-24.pptx) - [NYC Blockhain  Center ](https://blockchaincenter.nyc/)

`Presenters: Grant Steinfeld and Matthew Cousens`

[Worshop given 06/26/2019  Presentation deck](https://github.com/Grant-Steinfeld/hlf-private-data/blob/master/WORKSHOP-Hyperledger-Fabric-for-the-Enterprise-Privacy-and-Security-Strategies-v2.0.0-2019-6-26.pptx) - [NYC Blockhain  Center ](https://blockchaincenter.nyc/)

`Presenter: Grant Steinfeld`



### prerequisites for Workshop pattern


A computer, Mac, Linux or Windows

For this repo, the Private data tutorial 
was setup on a MacBook Pro running v10.14.5 (Mojave)

## Latest update!
`Note follow the hyperledger fabric docs tutorial as these instructions below may be out of date!`

[Private Data Marbles collection[(https://hyperledger-fabric.readthedocs.io/en/release-1.4/private_data_tutorial.html)


---------------------------------------------

### Summary from Worshop given 06/26/2019

Setup environment (Mac) (Setup on RHEL7 failed)

#### We ran these commands as regular user

#### Install or check you have the following on your computer.

> cURL
> docker - ver 17.06.2-ce or greater
> docker-compose - ver 1.14.0 or greater 
> Go v 1.11.x
> Node.js - ver 8.x
> Python 2.7 
> git


```sh
#confirm docker version
docker version
Client:
 Version:           18.09.6
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        481bc77156
 Built:             Sat May  4 02:34:58 2019
 OS/Arch:           linux/amd64
 Experimental:      false

#confirm docker-compose version

docker-compose --version
docker-compose version 1.24.0, build 0aa59064
```


## Installing Prerequisites
For node we used [Node Version Manager NVM](https://github.com/nvm-sh/nvm#install--update-script) ( highly recommended )
```sh
sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash

#source your .bashrc file
. ~/.bashrc

#install node v8
nvm install v8.16.0
#set default
nvm alias default 8.16.0
#confirm node ver
node --version
v8.16.0

#confirm go version

go version
version go1.12.5 darwin/amd64


#confirm python version
python -V
Python 2.7.5
```

## Verification Prerequisites
### check your versions
```sh
node --version
python --version
docker version
docker_compose version
go version


### checkout the Hyperledger Fabric repo

```sh

git clone https://github.com/hyperledger/fabric.git

```

## Setting up 
[How to setup a HLF basic network](https://hyperledger-fabric.readthedocs.io/en/release-1.4/install.html)

``` sh
# create a directory where you want to install
# the binaries and configuration files
# e.g.
mkdir -p /Users/Grant.Steinfeld@ibm.com/Documents/dev/hlf-private-data-git
cd /Users/Grant.Steinfeld@ibm.com/Documents/dev/hlf-private-data-git
#script A1
curl -sSL http://bit.ly/2ysbOFE | bash -s

``` 

If all went well you should output as outlined in Appendix A

and see a new folder `fabric-samples` 
( see it's contents in Appendix B)

the script above (A1) will download and install all the binaries for your platform


```sh
fabric-samples/bin:(detached from v1.4.1)$ tree
.
├── configtxgen
├── configtxlator
├── cryptogen
├── discover
├── fabric-ca-client
├── idemixgen
├── orderer
└── peer
```

add the bin dir to your PATH env var
```sh
# export PATH=<path to download location>/bin:$PATH
# e.g.

export PATH=/Users/Grant.Steinfeld@ibm.com/Documents/dev/hlf-private-data-git/fabric-samples/bin:$PATH

```


### Now we have the HLF binaries installed we can proceed with the Tutorial

## The Private Data Tutorial

### 1. Build collection defintion JSON file
`The first step in privatizing data on a channel is to build a collection definition which defines access to the private data.`

who can persist data

distribution to which peers

blockToLive

create the collections_config.json file
```JSON
[
  {
       "name": "collectionMarbles",
       "policy": "OR('Org1MSP.member', 'Org2MSP.member')",
       "requiredPeerCount": 0,
       "maxPeerCount": 3,
       "blockToLive":1000000,
       "memberOnlyRead": true
  },

  {
       "name": "collectionMarblePrivateDetails",
       "policy": "OR('Org1MSP.member')",
       "requiredPeerCount": 0,
       "maxPeerCount": 3,
       "blockToLive":3,
       "memberOnlyRead": true
  }
]

```
TIP!  to find the file it's actually created for us in the samples

```sh
#find the JSON above
cd /Users/Grant.Steinfeld@ibm.com/Documents/dev/hlf-private-data-git/fabric-samples
grep -rlw "collectionMarbles" .

./chaincode/marbles02_private/collections_config.json
```

### 2. Read and Write private data using chaincode APIs

## SmartContract in this tutorial is written in Go


```sh
#find the Go data definitions
cd /Users/Grant.Steinfeld@ibm.com/Documents/dev/hlf-private-data-git/fabric-samples
grep -rlw "marblePrivateDetails" .
./chaincode/marbles02_private/go/marbles_chaincode_private.go

```


So this snippet shows how the data will be accessed.

marble -> seen by all peers in  Org (1 & 2)
marblePrivateDetails -> only peers in Org1

``` Go
// Peers in Org1 and Org2 will have this private data in a side database
type marble struct {
  ObjectType string `json:"docType"`
  Name       string `json:"name"`
  Color      string `json:"color"`
  Size       int    `json:"size"`
  Owner      string `json:"owner"`
}

// Only peers in Org1 will have this private data in a side database
type marblePrivateDetails struct {
  ObjectType string `json:"docType"`
  Name       string `json:"name"`
  Price      int    `json:"price"`
}

```
so `price` is only visible to members of Org1

Here is the diagram showing how Org1 peers can see `price`
![org1 see's price](https://hyperledger-fabric.readthedocs.io/en/release-1.4/_images/SideDB-org1.png)

and how Org2 peers do NOT see the sensitive data `price`

![org2 does not see price](https://hyperledger-fabric.readthedocs.io/en/release-1.4/_images/SideDB-org2.png)

# The demo for Private Data
#### demo parts of it especailly adding data to one Org1 and seeing how part of it is private to Org1
#### Org2 will not see it
#### additionally demo purging the private data based off block length


### Try the demo yourself!
By now you have seen the demo in action, try it out for yourself!

https://hyperledger-fabric.readthedocs.io/en/release-1.4/private_data_tutorial.html#pd-build-json


# The Fab Car demo on IBM Cloud (IBPv2.0) of certficate attribute decoration 
## e.g. use for client roles and permissions
[see video here(tbprepped_still.mp4)


## Resources
Use a Kubernetes cluster to deploy a Fabric network smart contract onto blockchain
- Angular client uses Node.js API to interact with the network
https://developer.ibm.com/patterns/write-a-smart-contract-for-the-fabcarcommercial-paper-or-iks-cluster-with-saas-v2-beta-network-think/

Hyperledger Fabric
HLF Tutorial Marble Data
https://hyperledger-fabric.readthedocs.io/en/release-1.4/private_data_tutorial.html


settting up Node Version Manager(NVM)
https://github.com/nvm-sh/nvm#install--update-script


## Troubleshooting
### cleanup old docker containers and images may be necessary

```sh
docker prune
docker rmi -f $(docker images -q)
```
restart docker as well

check versions

## Appendeces

### Appendix A


``` sh
~/dev/hlf-bin:$ curl -sSL http://bit.ly/2ysbOFE | bash -s

# output from installing the binaries

Installing hyperledger/fabric-samples repo

===> Cloning hyperledger/fabric-samples repo and checkout v1.4.1
Cloning into 'fabric-samples'...
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 3268 (delta 0), reused 4 (delta 0), pack-reused 3264
Receiving objects: 100% (3268/3268), 1.07 MiB | 0 bytes/s, done.
Resolving deltas: 100% (1599/1599), done.
Note: checking out 'v1.4.1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 6c0203a... FABCI-284 Update CI Pipeline script

Installing Hyperledger Fabric binaries

===> Downloading version 1.4.1 platform specific fabric binaries
===> Downloading:  https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric/hyperledger-fabric/linux-amd64-1.4.1/hyperledger-fabric-linux-amd64-1.4.1.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 51.9M  100 51.9M    0     0  13.4M      0  0:00:03  0:00:03 --:--:-- 13.4M
==> Done.
===> Downloading version 1.4.1 platform specific fabric-ca-client binary
===> Downloading:  https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/linux-amd64-1.4.1/hyperledger-fabric-ca-linux-amd64-1.4.1.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 6323k  100 6323k    0     0  4416k      0  0:00:01  0:00:01 --:--:-- 4418k
==> Done.

Installing Hyperledger Fabric docker images

===> Pulling fabric Images
==> FABRIC IMAGE: peer

1.4.1: Pulling from hyperledger/fabric-peer
Digest: sha256:05315d05b2892d34b4ed48f6502d28fe15a71090c36a39c97022a44475a984ad
Status: Image is up to date for hyperledger/fabric-peer:1.4.1
==> FABRIC IMAGE: orderer

1.4.1: Pulling from hyperledger/fabric-orderer
Digest: sha256:09f31ca4dabe1eb2af870ea062561ca6686fc59a296ecc3b4bd7e32102c48934
Status: Image is up to date for hyperledger/fabric-orderer:1.4.1
==> FABRIC IMAGE: ccenv

1.4.1: Pulling from hyperledger/fabric-ccenv
Digest: sha256:bb929eef560b50e0fbd730c6b195e49fece28dd4612ec30db0ce2cc096483463
Status: Image is up to date for hyperledger/fabric-ccenv:1.4.1
==> FABRIC IMAGE: tools

1.4.1: Pulling from hyperledger/fabric-tools
Digest: sha256:c458ddc3109d3519b209baaf9abff113641267ec2adb01dfdcf8f4c9e77a2fa0
Status: Image is up to date for hyperledger/fabric-tools:1.4.1
==> FABRIC IMAGE: baseos

Error response from daemon: manifest for hyperledger/fabric-baseos:1.4.1 not found
Error response from daemon: No such image: hyperledger/fabric-baseos:1.4.1
==> FABRIC IMAGE: nodeenv

Error response from daemon: manifest for hyperledger/fabric-nodeenv:1.4.1 not found
Error response from daemon: No such image: hyperledger/fabric-nodeenv:1.4.1
==> FABRIC IMAGE: javaenv

1.4.1: Pulling from hyperledger/fabric-javaenv
Digest: sha256:9f4a99b36f33f4071f34f295f6a440ff51e22393ef5458e69c12fad897ae46f8
Status: Image is up to date for hyperledger/fabric-javaenv:1.4.1
===> Pulling fabric ca Image
==> FABRIC CA IMAGE

1.4.1: Pulling from hyperledger/fabric-ca
Digest: sha256:f77aa0ff885c572b090d1ff7564780daafd50d9e839b6241c2ab12c37f47b94a
Status: Image is up to date for hyperledger/fabric-ca:1.4.1
===> Pulling thirdparty docker images
==> THIRDPARTY DOCKER IMAGE: couchdb

0.4.15: Pulling from hyperledger/fabric-couchdb
Digest: sha256:f6c724592abf9c2b35d2f4cd6a7afcde9c1052cfed61560b20ef9e2e927d1790
Status: Image is up to date for hyperledger/fabric-couchdb:0.4.15
==> THIRDPARTY DOCKER IMAGE: kafka

0.4.15: Pulling from hyperledger/fabric-kafka
Digest: sha256:62418a885c291830510379d9eb09fbdd3d397052d916ed877a468b0e2026b9e3
Status: Image is up to date for hyperledger/fabric-kafka:0.4.15
==> THIRDPARTY DOCKER IMAGE: zookeeper

0.4.15: Pulling from hyperledger/fabric-zookeeper
Digest: sha256:180553e88d09167370aa62a41587a9a95b819b981ad74cad218689412b85f130
Status: Image is up to date for hyperledger/fabric-zookeeper:0.4.15

===> List out hyperledger docker images
hyperledger/fabric-javaenv                                                                 amd64-latest        e4db68de4ff2        9 days ago          1.22MB
hyperledger/fabric-ca                                                                      1.4.1               3a1799cda5d7        2 months ago        252MB
hyperledger/fabric-ca                                                                      latest              3a1799cda5d7        2 months ago        252MB
hyperledger/fabric-tools                                                                   1.4.1               432c24764fbb        2 months ago        1.55GB
hyperledger/fabric-tools                                                                   latest              432c24764fbb        2 months ago        1.55GB
hyperledger/fabric-ccenv                                                                   1.4.1               d7433c4b2a1c        2 months ago        1.43GB
hyperledger/fabric-ccenv                                                                   latest              d7433c4b2a1c        2 months ago        1.43GB
hyperledger/fabric-orderer                                                                 1.4.1               ec4ca236d3d4        2 months ago        173MB
hyperledger/fabric-orderer                                                                 latest              ec4ca236d3d4        2 months ago        173MB
hyperledger/fabric-peer                                                                    1.4.1               a1e3874f338b        2 months ago        178MB
hyperledger/fabric-peer                                                                    latest              a1e3874f338b        2 months ago        178MB
hyperledger/fabric-javaenv                                                                 1.4.1               b8c9d7ff6243        2 months ago        1.74GB
hyperledger/fabric-javaenv                                                                 latest              b8c9d7ff6243        2 months ago        1.74GB
hyperledger/fabric-zookeeper                                                               latest              20c6045930c8        3 months ago        1.43GB
hyperledger/fabric-kafka                                                                   0.4.15              b4ab82bbaf2f        3 months ago        1.44GB
hyperledger/fabric-kafka                                                                   latest              b4ab82bbaf2f        3 months ago        1.44GB
hyperledger/fabric-couchdb                                                                 0.4.15              8de128a55539        3 months ago        1.5GB
hyperledger/fabric-couchdb                                                                 latest              8de128a55539        3 months ago        1.5GB
hyperledger/fabric-baseos                                                                  amd64-0.4.15        9d6ec11c60ff        3 months ago        145MB
hyperledger/fabric-orderer                                                                 x86_64-1.1.0        ce0c810df36a        15 months ago       180MB
hyperledger/fabric-peer

```
End example out put

### Appendix B

```sh
```sh
cd fabric-samples
ls -a 

basic-network
chaincode-docker-devmode
commercial-paper
docs
.git
.gitreview
Jenkinsfile
README.md
bin
ci.properties
config
fabcar
.github
high-throughput
LICENSE
scripts
balance-transfer
chaincode
CODE_OF_CONDUCT.md
CONTRIBUTING.md
first-network
.gitignore
interest_rate_swaps
MAINTAINERS.md

```
