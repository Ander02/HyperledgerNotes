# Hyperledger Fabric Network Desing & Setup

## Cryptotxgen Tool 

- Utility for managing configuration artifacts 

- Genesis Block
    - The first block in the blockchaiun

- Channel Tx
    - Create channel Transaction

- Anchor Peer
    - Update Anchor Peer transaction

- Has two type of commands 
    - Output 
        - Requires a configuration .yaml file for generate a artifact output
    - Inspectikon
        - Outputs configuration as Json

- Transactions may be signed by multiple admins using Peers

-----------------------------------------------------------------------------

## The Configuration file

- The file is configured in a Environment Variable named `FABRIC_CFG_PATH` with point the folder where the configuration file is localizaded
    - If this variable not set, the default is the current folder plus configtx.yaml file

- All properties can be overridden by setting Environmments Variables
    - for example, change orderer service between solo and kafka: 
        - `export CONFIG_ORDERER_ORDERERTYPE=solo`
        - `export CONFIG_ORDERER_ORDERERTYPE=kafka`

### The structure

- The variables and organizations are defined as anchors(&) to avoid repetitions
- The configuration file has Six sections

1. The organizations
    - List the member organizations and be referenced by another sections
1. Orderer
    - The orderer configuration and setup
1. Application
    - Set the application configuration
1. Channel
    - Set channel parameters
1. Capabilities
    - Allow binary version management
1. Profiles
    - Setup multiples configs in a single file 

### The file - Organizations

- This section defines the organizational identities that can be referenced in the configuration profiles.

~~~~yaml
Organizations:

    # SampleOrg defines an MSP using the sampleconfig. It should never be used
    # in production but may be used as a template for other definitions.
    - &SampleOrg
        # Name is the key by which this org will be referenced in channel
        # configuration transactions.
        # Name can include alphanumeric characters as well as dots and dashes.
        Name: SampleOrg

        # ID is the key by which this org's MSP definition will be referenced.
        # ID can include alphanumeric characters as well as dots and dashes.
        ID: SampleOrg

        # MSPDir is the filesystem path which contains the MSP configuration.
        MSPDir: msp

        # Policies defines the set of policies at this level of the config tree
        # For organization policies, their canonical path is usually
        #   /Channel/<Application|Orderer>/<OrgName>/<PolicyName>
        Policies: &SampleOrgPolicies
            Readers:
                Type: Signature
                Rule: "OR('SampleOrg.member')"
                # If your MSP is configured with the new NodeOUs, you might
                # want to use a more specific rule like the following:
                # Rule: "OR('SampleOrg.admin', 'SampleOrg.peer', 'SampleOrg.client')"
            Writers:
                Type: Signature
                Rule: "OR('SampleOrg.member')"
                # If your MSP is configured with the new NodeOUs, you might
                # want to use a more specific rule like the following:
                # Rule: "OR('SampleOrg.admin', 'SampleOrg.client')"
            Admins:
                Type: Signature
                Rule: "OR('SampleOrg.admin')"

        # AnchorPeers defines the location of peers which can be used for
        # cross-org gossip communication. Note, this value is only encoded in
        # the genesis block in the Application section context.
        AnchorPeers:
            - Host: 127.0.0.1
              Port: 7051
~~~~

### The file - Orderer

- This section defines the values to encode into a config transaction or genesis block for orderer related parameters.

~~~yaml
Orderer: &OrdererDefaults

    # Orderer Type: The orderer implementation to start.
    # Available types are "solo" and "kafka".
    OrdererType: solo

    # Addresses here is a nonexhaustive list of orderers the peers and clients can
    # connect to. Adding/removing nodes from this list has no impact on their
    # participation in ordering.
    # NOTE: In the solo case, this should be a one-item list.
    Addresses:
        - 127.0.0.1:7050
    
    Kafka:
        # Brokers: A list of Kafka brokers to which the orderer connects. Edit
        # this list to identify the brokers of the ordering service.
        # NOTE: Use IP:port notation.
        Brokers:
            - kafka0:9092
            - kafka1:9092
            - kafka2:9092

    # Batch Timeout: The amount of time to wait before creating a batch.
    BatchTimeout: 2s

    # Batch Size: Controls the number of messages batched into a block.
    BatchSize:

        # Max Message Count: The maximum number of messages to permit in a
        # batch.
        MaxMessageCount: 10

        # Absolute Max Bytes: The absolute maximum number of bytes allowed for
        # the serialized messages in a batch. If the "kafka" OrdererType is
        # selected, set 'message.max.bytes' and 'replica.fetch.max.bytes' on
        # the Kafka brokers to a value that is larger than this one.
        AbsoluteMaxBytes: 10 MB

        # Preferred Max Bytes: The preferred maximum number of bytes allowed
        # for the serialized messages in a batch. A message larger than the
        # preferred max bytes will result in a batch larger than preferred max
        # bytes.
        PreferredMaxBytes: 512 KB

    # Max Channels is the maximum number of channels to allow on the ordering
    # network. When set to 0, this implies no maximum number of channels.
    MaxChannels: 0


    # Organizations lists the orgs participating on the orderer side of the
    # network.
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Orderer policies, their canonical path is
    #   /Channel/Orderer/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        # BlockValidation specifies what signatures must be included in the block
        # from the orderer for the peer to validate it.
        BlockValidation:
            Type: ImplicitMeta
            Rule: "ANY Writers"

    # Capabilities describes the orderer level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *OrdererCapabilities
~~~

### The file - Application

~~~yaml
Application: &ApplicationDefaults
    ACLs: &ACLsDefault
        # This section provides defaults for policies for various resources
        # in the system. These "resources" could be functions on system chaincodes
        # (e.g., "GetBlockByNumber" on the "qscc" system chaincode) or other resources
        # (e.g.,who can receive Block events). This section does NOT specify the resource's
        # definition or API, but just the ACL policy for it.
        #
        # User's can override these defaults with their own policy mapping by defining the
        # mapping under ACLs in their channel definition

        #---Lifecycle System Chaincode (lscc) function to policy mapping for access control---#

        # ACL policy for lscc's "getid" function
        lscc/ChaincodeExists: /Channel/Application/Readers

        # ACL policy for lscc's "getdepspec" function
        lscc/GetDeploymentSpec: /Channel/Application/Readers

        # ACL policy for lscc's "getccdata" function
        lscc/GetChaincodeData: /Channel/Application/Readers

        # ACL Policy for lscc's "getchaincodes" function
        lscc/GetInstantiatedChaincodes: /Channel/Application/Readers

        #---Query System Chaincode (qscc) function to policy mapping for access control---#

        # ACL policy for qscc's "GetChainInfo" function
        qscc/GetChainInfo: /Channel/Application/Readers

        # ACL policy for qscc's "GetBlockByNumber" function
        qscc/GetBlockByNumber: /Channel/Application/Readers

        # ACL policy for qscc's  "GetBlockByHash" function
        qscc/GetBlockByHash: /Channel/Application/Readers

        # ACL policy for qscc's "GetTransactionByID" function
        qscc/GetTransactionByID: /Channel/Application/Readers

        # ACL policy for qscc's "GetBlockByTxID" function
        qscc/GetBlockByTxID: /Channel/Application/Readers

        #---Configuration System Chaincode (cscc) function to policy mapping for access control---#

        # ACL policy for cscc's "GetConfigBlock" function
        cscc/GetConfigBlock: /Channel/Application/Readers

        # ACL policy for cscc's "GetConfigTree" function
        cscc/GetConfigTree: /Channel/Application/Readers

        # ACL policy for cscc's "SimulateConfigTreeUpdate" function
        cscc/SimulateConfigTreeUpdate: /Channel/Application/Readers

        #---Miscellanesous peer function to policy mapping for access control---#

        # ACL policy for invoking chaincodes on peer
        peer/Propose: /Channel/Application/Writers

        # ACL policy for chaincode to chaincode invocation
        peer/ChaincodeToChaincode: /Channel/Application/Readers

        #---Events resource to policy mapping for access control###---#

        # ACL policy for sending block events
        event/Block: /Channel/Application/Readers

        # ACL policy for sending filtered block events
        event/FilteredBlock: /Channel/Application/Readers

    # Organizations lists the orgs participating on the application side of the
    # network.
    Organizations:

    # Policies defines the set of policies at this level of the config tree
    # For Application policies, their canonical path is
    #   /Channel/Application/<PolicyName>
    Policies: &ApplicationDefaultPolicies
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"

    # Capabilities describes the application level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ApplicationCapabilities
~~~

### The file - Channel

- This section defines the values to encode into a config transaction or genesis block for channel related parameters.

~~~yaml
Channel: &ChannelDefaults
    # Policies defines the set of policies at this level of the config tree
    # For Channel policies, their canonical path is
    #   /Channel/<PolicyName>
    Policies:
        # Who may invoke the 'Deliver' API
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        # Who may invoke the 'Broadcast' API
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        # By default, who may modify elements at this config level
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"


    # Capabilities describes the channel level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ChannelCapabilities
~~~

### The file - Profile

-  Different configuration profiles may be encoded here to be specified as parameters to the configtxgen tool. The profiles which specify consortiums are to be used for generating the orderer genesis block. With the correct consortium members defined in the orderer genesis block, channel creation requests may be generated with only the org member names and a consortium name.

~~~yaml

Profiles:

    # SampleSingleMSPSolo defines a configuration which uses the Solo orderer,
    # and contains a single MSP definition (the MSP sampleconfig).
    # The Consortium SampleConsortium has only a single member, SampleOrg.
    SampleSingleMSPSolo:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - *SampleOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                    - *SampleOrg

    # SampleSingleMSPKafka defines a configuration that differs from the
    # SampleSingleMSPSolo one only in that it uses the Kafka-based orderer.
    SampleSingleMSPKafka:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            OrdererType: kafka
            Organizations:
                - *SampleOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                    - *SampleOrg

    # SampleInsecureSolo defines a configuration which uses the Solo orderer,
    # contains no MSP definitions, and allows all transactions and channel
    # creation requests for the consortium SampleConsortium.
    SampleInsecureSolo:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
        Consortiums:
            SampleConsortium:
                Organizations:

    # SampleInsecureKafka defines a configuration that differs from the
    # SampleInsecureSolo one only in that it uses the Kafka-based orderer.
    SampleInsecureKafka:
        <<: *ChannelDefaults
        Orderer:
            OrdererType: kafka
            <<: *OrdererDefaults
        Consortiums:
            SampleConsortium:
                Organizations:

    # SampleDevModeSolo defines a configuration which uses the Solo orderer,
    # contains the sample MSP as both orderer and consortium member, and
    # requires only basic membership for admin privileges. It also defines
    # an Application on the ordering system channel, which should usually
    # be avoided.
    SampleDevModeSolo:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - <<: *SampleOrg
                  Policies:
                      <<: *SampleOrgPolicies
                      Admins:
                          Type: Signature
                          Rule: "OR('SampleOrg.member')"
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - <<: *SampleOrg
                  Policies:
                      <<: *SampleOrgPolicies
                      Admins:
                          Type: Signature
                          Rule: "OR('SampleOrg.member')"
        Consortiums:
            SampleConsortium:
                Organizations:
                    - <<: *SampleOrg
                      Policies:
                          <<: *SampleOrgPolicies
                          Admins:
                              Type: Signature
                              Rule: "OR('SampleOrg.member')"

    # SampleDevModeKafka defines a configuration that differs from the
    # SampleDevModeSolo one only in that it uses the Kafka-based orderer.
    SampleDevModeKafka:
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            OrdererType: kafka
            Organizations:
                - <<: *SampleOrg
                  Policies:
                      <<: *SampleOrgPolicies
                      Admins:
                          Type: Signature
                          Rule: "OR('SampleOrg.member')"
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - <<: *SampleOrg
                  Policies:
                      <<: *SampleOrgPolicies
                      Admins:
                          Type: Signature
                          Rule: "OR('SampleOrg.member')"
        Consortiums:
            SampleConsortium:
                Organizations:
                    - <<: *SampleOrg
                      Policies:
                          <<: *SampleOrgPolicies
                          Admins:
                              Type: Signature
                              Rule: "OR('SampleOrg.member')"

    # SampleSingleMSPChannel defines a channel with only the sample org as a
    # member. It is designed to be used in conjunction with SampleSingleMSPSolo
    # and SampleSingleMSPKafka orderer profiles.   Note, for channel creation
    # profiles, only the 'Application' section and consortium # name are
    # considered.
    SampleSingleMSPChannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *SampleOrg
~~~

## Genesis Block

- The genesis block is the first blockcin the blockchain and is used for launching the Orderer

