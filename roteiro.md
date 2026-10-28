# 1. Instalar Requisitos

### 1.1 Aplicativos de Suporte

```bash
sudo apt install -y zip unzip
```

### 1.2. Instalação do Docker

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Para não precisar usar sudo em todos os comandos, execute:


```bash
sudo usermod -aG docker vagrant
newgrp docker
```

Para confirma a correta instalação e versão do Docker

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
docker --version
sudo docker run hello-world
```

### 1.3. Docker Compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.3/docker-compose-linux-x86_64" -o docker-compose
sudo cp docker-compose /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose
```
Para confirmar a correta instalação e versão do Docker Compose

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
docker-compose --version
```

### 1.4. Java 17

```bash
apt-cache search openjdk | grep openjdk-17
sudo apt install -y openjdk-17-jre
sudo apt install -y openjdk-17-jdk
```

Para confirma a correta instalação e versão do Java

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
java --version
```


```bash
grep -qxF 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' ~/.bashrc || \
echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' >> ~/.bashrc

grep -qxF 'export PATH=$JAVA_HOME/bin:$PATH' ~/.bashrc || \
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc

source ~/.bashrc
```

Para exibir o valor da variável de ambiente JAVA_HOME

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
echo $JAVA_HOME
```

---

# 2. Instalar os binários do Besu​

### 2.1 Criar pastas

```bash
cd
mkdir rede-besu
cd rede-besu
mkdir -p bin
mkdir -p config/besu
mkdir -p config/nodes
```

## 2.2 Download do Hyperledger Besu

```bash
cd ~/rede-besu/bin
curl -L https://github.com/hyperledger/besu/releases/download/24.5.4/besu-24.5.4.zip -o besu-24.5.4.zip
unzip besu-24.5.4.zip
```

## 2.3 Adicionar o Besu ao PATH

```bash
grep -qxF 'export PATH="$HOME/rede-besu/bin/besu-24.5.4/bin:$PATH"' ~/.bashrc || \
echo 'export PATH="$HOME/rede-besu/bin/besu-24.5.4/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
Para verificar versão instalada e os parâmetros disponíveis (besu [OPTIONS])

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
besu --version
besu --help
```
---
# 3. Gerar as chaves criptográficas dos nodes

Configure as variáveis de ambiente:

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
BASEDIR=/home/<usuario>/rede-besu
CONFIGDIR=$BASEDIR/config
NODESDIR=$CONFIGDIR/nodes
cd $BASEDIR/bin
```
As chaves criptográficas dos validadores da rede já foram previamente geradas para este laboratório. Entretanto, caso fosse necessário criá-las manualmente, os seguintes comandos poderiam ser utilizados:

## Org1 Node1

```bash
mkdir $NODESDIR/org1-node1
besu --data-path=$NODESDIR/org1-node1 public-key export --to=$NODESDIR/org1-node1/key.pub
besu --data-path=$NODESDIR/org1-node1 public-key export-address --to=$NODESDIR/org1-node1/node.id
```

## Org1 Node2

```bash
mkdir $NODESDIR/org1-node2
besu --data-path=$NODESDIR/org1-node2 public-key export --to=$NODESDIR/org1-node2/key.pub
bbesu --data-path=$NODESDIR/org1-node2 public-key export-address --to=$NODESDIR/org1-node2/node.id
```

## Org2 Node1

```bash
mkdir $NODESDIR/org2-node1
besu --data-path=$NODESDIR/org2-node1 public-key export --to=$NODESDIR/org2-node1/key.pub
besu --data-path=$NODESDIR/org2-node1 public-key export-address --to=$NODESDIR/org2-node1/node.id
```

## Org2 Node2

```bash
mkdir $NODESDIR/org2-node2
besu --data-path=$NODESDIR/org2-node2 public-key export --to=$NODESDIR/org2-node2/key.pub
besu --data-path=$NODESDIR/org2-node2 public-key export-address --to=$NODESDIR/org2-node2/node.id
```

## Org3 Node1

```bash
mkdir $NODESDIR/org3-node1
besu --data-path=$NODESDIR/org3-node1 public-key export --to=$NODESDIR/org3-node1/key.pub
besu --data-path=$NODESDIR/org3-node1 public-key export-address --to=$NODESDIR/org3-node1/node.id
```

## Org3 Node2

```bash
mkdir $NODESDIR/org3-node2
besu --data-path=$NODESDIR/org3-node2 public-key export --to=$NODESDIR/org3-node2/key.pub
besu --data-path=$NODESDIR/org3-node2 public-key export-address --to=$NODESDIR/org3-node2/node.id
```
---
## 4. Gerar os arquivos de configuração da blockchain
Nesta etapa, serão criados os arquivos genesis.json e static-nodes.json.

O genesis.json define os parâmetros iniciais da blockchain, incluindo o consenso QBFT e os validadores iniciais.

O static-nodes.json contém os enode URLs dos nós com os quais cada nó tentará estabelecer conexões estáticas.

## 4.1 Configurar as variáveis de ambiente

```bash
export BASEDIR="$HOME/rede-besu"
export CONFIGDIR="$BASEDIR/config"
export NODESDIR="$CONFIGDIR/nodes"
```
Crie o diretório de configuração do Besu, caso ainda não exista:


```bash
mkdir -p "$CONFIGDIR/besu"
```

## 4.2 Gerar o arquivo validators.json
Primeiro, leia os endereços dos seis nós a partir dos arquivos gerados na etapa 3:


```bash
ORG1_NODE1=$(cat "$NODESDIR/org1-node1/node.id")
ORG1_NODE2=$(cat "$NODESDIR/org1-node2/node.id")
ORG2_NODE1=$(cat "$NODESDIR/org2-node1/node.id")
ORG2_NODE2=$(cat "$NODESDIR/org2-node2/node.id")
ORG3_NODE1=$(cat "$NODESDIR/org3-node1/node.id")
ORG3_NODE2=$(cat "$NODESDIR/org3-node2/node.id")
```

Agora, gere o arquivo JSON:


```bash
cat > "$CONFIGDIR/besu/validators.json" <<EOF
[
  "$ORG1_NODE1",
  "$ORG1_NODE2",
  "$ORG2_NODE1",
  "$ORG2_NODE2",
  "$ORG3_NODE1",
  "$ORG3_NODE2"
]
EOF
```

Confira o resultado:


```bash
cat "$CONFIGDIR/besu/validators.json"
```
Valide se o JSON está correto:


```bash
python3 -m json.tool "$CONFIGDIR/besu/validators.json"
```

Gerar o extraData
Com o arquivo pronto, execute:


```bash
besu rlp encode \
  --from="$CONFIGDIR/besu/validators.json" \
  --type=QBFT_EXTRA_DATA
```

## 4.3 Criar o arquivo genesis.json

Crie o arquivo:

```bash
nano "$CONFIGDIR/besu/genesis.json"
```

Insira a configuração abaixo, substituindo 0xCOLE_AQUI_O_EXTRADATA_GERADO pelo resultado real do comando anterior.


```json
{
  "config": {
    "chainId": 1337,
    "berlinBlock": 0,
    "qbft": {
      "blockperiodseconds": 2,
      "epochlength": 30000,
      "requesttimeoutseconds": 4
    }
  },
  "nonce": "0x0",
  "timestamp": "0x0",
  "gasLimit": "0x1c9c380",
  "difficulty": "0x1",
  "mixHash": "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "extraData": "0xCOLE_AQUI_O_EXTRADATA_GERADO",
  "alloc": {}
}
```

Salve o arquivo.



```bash
python3 -m json.tool "$CONFIGDIR/besu/genesis.json"
```
## 4.4  Criar o arquivo static-nodes.json de cada nó
Cada nó terá seu próprio arquivo com os outros cinco nós da rede. Não inclua o próprio nó na lista e nem o 0x.

Crie o arquivo base:


```bash
nano "$NODESDIR/base-static-nodes.json"
```

Insira os enode URLs reais dos outros cinco nós:


```json
[
  "enode://CHAVE_ORG1_NODE2@org1-node1:30303",
  "enode://CHAVE_ORG1_NODE2@org1-node2:30303",
  "enode://CHAVE_ORG2_NODE1@org2-node1:30303",
  "enode://CHAVE_ORG2_NODE2@org2-node2:30303",
  "enode://CHAVE_ORG3_NODE1@org3-node1:30303",
  "enode://CHAVE_ORG3_NODE2@org3-node2:30303"
]
```

Em cada arquivo, mantem os outros cinco nós e remova a entrada correspondente ao próprio nó.:


``` bash
for i in {1..3}; do
  for j in {1..2}; do
    grep -v "org${i}-node${j}" "$NODESDIR/base-static-nodes.json" \
      | sed '$ s/,$//' \
      > "$NODESDIR/org${i}-node${j}/static-nodes.json"
  done
done

```



[!IMPORTANT]
Os nomes org1-node1, org1-node2 etc. só funcionarão como hosts se os contêineres compartilharem uma rede Docker na qual esses nomes possam ser resolvidos. Se as organizações forem executadas em máquinas distintas, utilize endereços IP ou DNS alcançáveis e as portas P2P correspondentes.

## 4.5 Validar os arquivos de configuração

Valide os arquivos JSON de cada nó:


``` bash
for i in {1..3}; do
  for j in {1..2}; do
    python3 -m json.tool "$NODESDIR/org$i-node$j/static-nodes.json"
  done
done
```

Esses comandos verificam a sintaxe JSON, mas não confirmam que as chaves, os hosts e as portas dos enode URLs estejam corretos.

## 5. Gerar os arquivos Docker Compose dos nodes
Nesta etapa, será criado um arquivo Docker Compose para a Org1, contendo os serviços org1-node1 e org1-node2.

## 5.1 Criar os diretórios de dados
Cada nó precisa de um diretório exclusivo para armazenar os dados da blockchain:


```bash
mkdir -p "$BASEDIR/volumes/org1-node1"
mkdir -p "$BASEDIR/volumes/org1-node2"
```

## 5.2 Criar a rede Docker compartilhada
Crie uma rede para permitir a comunicação entre os nós das três organizações:


```bash
docker network create rede-besu-net
```

Se a rede já existir, não é necessário criá-la novamente.

## 5.3 Criar o arquivo Docker Compose da Org1
Crie o arquivo:

```bash
nano "$BASEDIR/docker-compose-org1.yml"
```

Insira a configuração:


```yaml

services:
  org1-node1:
    image: hyperledger/besu:24.5.4
    container_name: org1-node1
    restart: unless-stopped

    environment:
      TZ: America/Sao_Paulo

    command:
      - --data-path=/var/lib/besu
      - --genesis-file=/config/genesis.json
      - --node-private-key-file=/var/lib/besu/key
      - --static-nodes-file=/config/static-nodes.json
      - --p2p-host=0.0.0.0
      - --p2p-port=30303
      - --rpc-http-enabled=true
      - --rpc-http-host=0.0.0.0
      - --rpc-http-port=8545
      - --rpc-http-api=ADMIN,ETH,NET,QBFT,WEB3
      - --rpc-http-cors-origins=*
      - --host-allowlist=*
      - --metrics-enabled=true
      - --metrics-host=0.0.0.0
      - --metrics-port=9545
      - --logging=DEBUG

    volumes:
      - ./volumes/org1-node1:/var/lib/besu
      - ./config/nodes/org1-node1/key:/var/lib/besu/key:ro
      - ./config/nodes/org1-node1/static-nodes.json:/config/static-nodes.json:ro
      - ./config/besu/genesis.json:/config/genesis.json:ro

    ports:
      - "30301:30303/tcp"
      - "30301:30303/udp"
      - "127.0.0.1:8545:8545"
      - "127.0.0.1:9545:9545"

    networks:
      - besu-net

  org1-node2:
    image: hyperledger/besu:24.5.4
    container_name: org1-node2
    restart: unless-stopped

    environment:
      TZ: America/Sao_Paulo

    command:
      - --data-path=/var/lib/besu
      - --genesis-file=/config/genesis.json
      - --node-private-key-file=/var/lib/besu/key
      - --static-nodes-file=/config/static-nodes.json
      - --p2p-host=0.0.0.0
      - --p2p-port=30303
      - --rpc-http-enabled=true
      - --rpc-http-host=0.0.0.0
      - --rpc-http-port=8545
      - --rpc-http-api=ADMIN,ETH,NET,QBFT,WEB3
      - --rpc-http-cors-origins=*
      - --host-allowlist=*
      - --metrics-enabled=true
      - --metrics-host=0.0.0.0
      - --metrics-port=9545
      - --logging=DEBUG

    volumes:
      - ./volumes/org1-node2:/var/lib/besu
      - ./config/nodes/org1-node2/key:/var/lib/besu/key:ro
      - ./config/nodes/org1-node2/static-nodes.json:/config/static-nodes.json:ro
      - ./config/besu/genesis.json:/config/genesis.json:ro

    ports:
      - "30302:30303/tcp"
      - "30302:30303/udp"
      - "127.0.0.1:8546:8545"
      - "127.0.0.1:9546:9545"

    networks:
      - besu-net

networks:
  besu-net:
    external: true
    name: rede-besu-net
```



## 5.4 Validar a configuração

```bash
cd "$BASEDIR"
docker compose -f docker-compose-org1.yml config
```
Confira os arquivos utilizados pelos dois nós:


```bash
ls -l config/besu/genesis.json
ls -l config/nodes/org1-node1/key
ls -l config/nodes/org1-node1/static-nodes.json
ls -l config/nodes/org1-node2/key
ls -l config/nodes/org1-node2/static-nodes.json
```
## 5.5 Iniciar os nós da Org1

```bash
docker compose -f docker-compose-org1.yml up -d
```
Verifique o estado dos serviços:


```bash
docker compose -f docker-compose-org1.yml ps
```
Acompanhe os logs:

```bash
docker compose -f docker-compose-org1.yml logs -f
```
Para consultar apenas o primeiro nó:


```bash
docker logs -f org1-node1
```
Para interromper os serviços:

```bash
docker compose -f docker-compose-org1.yml down
```
## 5.6 Validar a conectividade
Após iniciar as organizações, consulte os peers conectados ao primeiro nó da Org1:


```bash
curl -s http://127.0.0.1:8545 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}'
```

O resultado apresentará a quantidade de peers conectados em hexadecimal. Em uma rede com seis nós totalmente conectados, cada nó poderá ter cinco peers.
