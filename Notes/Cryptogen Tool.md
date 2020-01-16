# Hyperledger Fabric Network Desing & Setup

## Cryptogen Tool 

- Command line tool
- Requires the information in YAML format

- Generate crypto material of configuration file

- Used for *test* environment setups
- No use in production!
    - Expose identities and crypto material

- How to use
~~~bash
cryptogen command --flags <args>

cryptogen help

cryptogen command --help
~~~

- How to configure the configuration file

Orderers
~~~yaml

OrdererOrgs:
    - Name: OrdererName
      Domain: domain.com
      Specs:
        - Hostname: orderer hostname
~~~

Peers
~~~yaml
PeersOrgs:
    - Name: Org1
      Domain: domain.com
      Specs:
        - Hostname: peer hostname
          CommonName: peer common name
      Users:
        Count: 2

~~~

- The configuration file is used for command 

~~~bash
cryptogen generate --config=./crypto-config-file.yaml
~~~

Extend the original configuration

- New .yaml file with the new peers, orderers, users and memeber with be added

