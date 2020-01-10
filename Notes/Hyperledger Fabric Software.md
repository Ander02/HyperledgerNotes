# Hyperledger Fabric Network Desing & Setup

## Hyperledger Fabric Software

- Dependencies
    - GoLang
        - The main code of hyperledger implementation is in GoLang
    - Docker and Docker Compose
        - Use by deploy the chaincode in isolated docker containers
        - The Hyperledger components are avaiable as Images
        - The Docker Container Ochestartion is used on Hyperledger Network
    - LevelDB and CouchDB
        - Store the blockchain data
        - LevelDb for Log and State
        - CouchDb for State how optional
    - Kafka
        - Queue service for Orderer Peers in Production
    - SOLO
        - Queue service for Orderer Peers in Development

- Components and Binaries
    - Core Components
        - Peer
        - Orderer
    - Tools and Utilities
        - configtxgen
        - cryptogen
        - configxlator
    - Fabric Certification Authority
        - fabric-ca-server
        - fabric-ca-client
_All the components require configuration information proviced in a .yaml file_

*Core Components*
- Peer
    - Launched as a process (node in the network)
        - Can be Tagged as Anchor or Leader
    - Used as tool to manage network and channels configuration
- Orderer
    - Messagin service 
        - Built-in SOLO
        - Can connect on Kafka

*Tools and Utilities*
- configtxgen
    - Management of network and channels configuration
- configxlator
    - Transalate the buffer in a Json format
- cryptogen
    - Generate crypto material for testing

*Fabric Certification Authority*
- fabric-ca-server
    - CA Server Implementation
        - Expose as services for manager identity and certifications
- fabric-ca-client
    - Command line tool for managing identities on CA Server

## How to Install

Docker
~~~Bash
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io
~~~

Vangrat
~~~Bash
sudo apt-get vangrat
~~~

GO
~~~Bash
Download from https://golang.org/dl/

tar -C /usr/local -xzf go1.13.6.linux-amd64.tar.gz

export PATH=$PATH:/usr/local/go/bin
~~~

Hyperledger Fabric
~~~Bash
curl -sSL http://bit.ly/2ysbOFE -o bootstrap.sh

chmod 755 ./bootstrap.sh

bash  ./bootstrap.sh  1.4.2 1.4.2 

echo "======= Copying the binaries to /usr/local/bin===="

mkdir -p  bin

cp fabric-samples/bin/*    /usr/local/bin
cp fabric-samples/bin/*    bin
rm -rf fabric-samples/bin

- The sample chaincode is under the subfolder go and need to come under gopath/src subfolder

echo "======= Setting up the GOPATH folder $GOPATH ===="
mkdir -p $GOPATH/src
cp -r fabric-samples/chaincode/*    $GOPATH/src
cp $GOPATH/src/chaincode_example02/go/chaincode_example02.go $GOPATH/src/chaincode_example02

echo "======= Setting up the Node chaincode folder ==="
mkdir -p $GOPATH/../nodechaincode/chaincode_example02
cp -r fabric-samples/chaincode/chaincode_example02/node/*    $GOPATH/../nodechaincode/chaincode_example02

- This downloads the shim code 
echo "======= Setting up the HLF Shim (Takes time  - Get a Coffee :)===="
go get -v -u github.com/hyperledger/fabric-chaincode-go/shim

~~~

CA Server
~~~Bash
export PATH=$PATH:$GOROOT/bin

- Sets up the fabric-ca-server & fabric-ca-client
echo "=====Installing libtool library"
sudo apt install -y libtool libltdl-dev


echo "=====Getting fabric ca "
- Document process leads to errors as it leads to pulling of master branch
go get -u github.com/hyperledger/fabric-ca/cmd/...

/*
git clone --branch release-1.3 https://github.com/hyperledger/fabric-ca.git
rm -rf $GOPATH/src/github.com/hyperledger/fabric-ca 2> /dev/null
mv fabric-ca  $GOPATH/src/github.com/hyperledger
go install github.com/hyperledger/fabric-ca/cmd/...
*/

echo "=====Copying fabric ca binaries"
sudo cp $GOPATH/bin/*    /usr/local/bin

sudo cp $GOPATH/bin/*    $PWD/../bin

sudo rm $GOPATH/bin/* 

echo "Done."
~~~

Jq
~~~Bash
sudo apt-get install -y jq
~~~