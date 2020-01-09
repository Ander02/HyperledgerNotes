# Hyperledger Fabric Network Desing & Setup

## Hyperledger Fabric Distribution and Infrastructure options

Infraestructure Setup
- Container Based Setup
    - Use Hyperledger Fabric Components docker images
    - Containers may be hosted in shared or dedicated VMs
    - Setup uses docker-compose
    - Can be possible use Kubernetes
 - Native Setup
    - Install separated binaries in each virtual machine

| *Container Based*         | *Native Based Setup*                      |
| -                         | -                                         |
| Docker Compose            | Scripts & DevOps Tooling                  |
| Faster to Setup           | Requires installation on Multiples VMs    |
| Hide details / complex    | Simple to understand                      |

Choice one depends of the scenario