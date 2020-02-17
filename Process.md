1. Gerar configtx.yaml -> Configuração geral da Blockchain

2. Gerar chaves para a rede com base no arquivo configtx.yaml
    - Pode-se usar cryptogen ou ca-server
    
3. Gerar orderer.yaml -> Configuração do nó ordenador da rede

4. Configurar variáveis de ambiente no orderer
    - `FABRIC_CFG_PATH`: Pasta onde está localizado os arquivos de configuração, em especial o configtx.yaml

5. Gerar bloco gênesis

6. Gerar canal do orderer 

7. Executar o orderer (comando: `orderer`)

------

8. Escrever core.yaml -> Configuração geral do Peer

9. Configurar varíavel de ambiente da localização do orderer
    - `CONFIG_DIRECTORY`: Pasta onde está localizado os arquivos de configuração, em especial o configtx.yaml
    - `FABRIC_CFG_PATH`: Pasta onde está localizado os arquivos de configuração do peer
    - `FABRIC_LOGGING_SPEC`: Tipo de Logging
    - `CORE_PEER_LISTENADDRESS`: Endereço do Peer
    - `CORE_PEER_ADDRESS` : Endereço de endpoint do Peer
    - `CORE_PEER_FILESYSTEMPATH`: Localzação na qual o peer savará os ledgers
    - `CORE_PEER_MSPCONFIGPATH` : MSP da configuração de identidade do Peer
    - `CORE_LEDGER_STATE_STATEDATABASE`: Tipo de banco de dados
    - `CORE_LEDGER_STATE_COUCHDBCONFIG_COUCHDBADDRESS`: Endereço do banco de dados
    - `CORE_LEDGER_STATE_COUCHDBCONFIG_USERNAME`: Username do banco
    - `CORE_LEDGER_STATE_COUCHDBCONFIG_PASSWORD`: Senha do banco

10. Verificar se o orderer está rodando
    - `pgrep -x "orderer"` 

11. Verificar se o arquivo do canal para com o orderer foi criado
    - `peer channel fetch 0 -o localhost:7050  -c <channel_name>  <channel_name>.block`

12. Se o canal não foi criado, criar o canal de comunicação com o orderer
    - `peer channel create -o $ORDERER_ADDRESS -c <channel_name> -f $CONFIG_DIRECTORY/acme-channel.tx`

13. Executar o peer
    - `peer node start`
    - Usar o `&` no final da linha para rodar em background

14. Entrar em um canal da rede
    - `peer channel join -o $ORDERER_ADDRESS -b ./<channel_name>.block`

15. Listar canais do peer
    - `peer channel list`
