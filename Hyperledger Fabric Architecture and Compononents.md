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
