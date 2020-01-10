# Hyperledger Fabric Network Desing & Setup

## Hyperledger Fabric Architecture and Compononents

- Blockchains are composed by nodes

- In public networks, all nodes are equals
- In Hyperledge, all nodes may not be equal.

Hyperledger has a concept of Members
- Members are Legally Separate Entities

Nodes in a Hyperledger, are Comunication entities
- Each Node is assingned by a certification
- Nodes provide the communication in the blockchain network

Ledgers manage and store Assets
- And assets represent anything in the real world
- Nodes initialize transactions
- Chaincode trigger transaction to register in the ledger

There is one ledger per channel in a blockchain
- Channels are a mode to grants privactity

- Members participate of a channel and are Certificated by a Membership Service Provider and Certification Authority

Has Three Type of Nodes ina Hyperledger Network
- Orderes
- Peers
    - Leader Peer
    - Anchor Peer
    - Regular Peer
- Client

Orderes
- Communication channel for Fabric
- Responsible for consistent ledger state across network
- Provides:
    - Consensus Mechanism
    - Ensure order of transactions
- This peers Create Blocks and Guarantees atomic delivery

- Implementation
    - Orderers is implemented with message oriented middleware
    - The Message Oriented Middleware can be
        - SOLO
            - Single node = Good for development
            - Single point of failure
        - Kafka
            - Clustering for high throughput, scalability & fault tolerance

Peers
- Has and maintains its own copy of the ledger
    - Your ledger has a Transaction Log and State 
        - The Transaction Log is Immutable
            - Ever a transaction is registered, cannot be deleted or updated anymore
        - The Transaction Log use LevelDB to store data
        - The State can use LevelDB or CouchDB to store data
        - In the state database is possible run complex queries througth chaincode
- Each Peer expose services
    - This services can be accessed by CLients, Orderes, and Another Peers
    - GRPC and Gossip Protocol expose the services and make the communication
- The Anchor Peers, are Discoverable
    - Each Organization MUST have at least one anchor peer
- The Leader Peers receive blocks from Orderes
    - Leadership is at channel level
    - May be statically assigned or dynamically assigned how leader

Client
- Act on end user side
- Are builded with Go or Node SDK
- Create Transaction Requests and submits to the network

Channels
- How to grants privacity on the network
- Transaction are isolated within the channel
- Chaincode is deployed at a channel and NOT in the Network
- Exists a Special Channel: Ordering System Channel or Bootstrap Channel
    - Is created on network initialization automatically
- Organizations and Peers may join in multiple channels, and each channel has your own distribuited ledger and state dabase

Public Key Infraestructure
- Identity Management in the hyperledgher flow the typical process for identity management
    0. User -> Register in a Registration Authoriry and Generate a Certificate in a Certification Authority
    0. User Receive the certificate
    0. Whenever a user use the certificate, it is validated by a Validation Authority
- In the hyperledger the Certification Authority is implemented by CA Server, with contains
    - Identity Registration
    - Identity Enrollment
    - Certificate Management