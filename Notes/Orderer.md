# Hyperledger Fabric Network Desing & Setup

## Orderer Binary

- The orderer binary is responsible for send transactions on the network

- It can use SOLO for test 
    - SOLO is a single instance of a messaging service
- In production it uses Kafka Clusters as backbone

- The orderer binary needs the Genesis Block, The MSP and the YAML configuration file
- The orderer store the data in RAM/Memory or in File System

- The configuration path is managed by environment variable `FABRIC_CFG_PATH`, where the orderer.yaml file is located
- The order writes the log messages in the stderr
    - The envronment variable `FABRIC_LOGGING_SPEC` control the log level
    - The log format is controled by `FABRIC_LOGGING_FORMAT`

### Orderer Configuration File

- This file has many sections

- General: General properties of the Orderer
- FileLedger or RAMLedger: Depending of type of orderer to use
- Debug: Debug configurations
- Operations: Operations endpoints used for network monitoring
- Metrics: Configuration for collection of metrics emitted by orderer
- Consensus: Configuration of consensus plug-in

*All Configurations can be accessed and overwritted by environment variables for example ORDERER_<SECTION_NAME>_<PROPERTY_NAME>*

### Crypto

- BCCSP: Blockchain Crypyo Service Provider
- BCCSP Setup MUST be consistent on entire network