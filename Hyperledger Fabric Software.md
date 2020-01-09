# Hyperledger Fabric Network Desing & Setup

## Hyperledger Fabric Software

- Dependencies
    - GoLand
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

