# 1. Instalar Requisitos

### 1.1 Aplicativos de Suporte

```bash
sudo apt install -y zip unzip jq
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
docker run hello-world
docker compose --version
```

### 1.3. Java 17

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

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

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
  "genesis": {
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
    "timestamp": "0x58ee40ba",
    "gasLimit": "0x47b760",
    "difficulty": "0x1",
    "mixHash": "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "extraData": "0xCOLE_AQUI_O_EXTRADATA_GERADO",
    "alloc": {
      "fe3b557e8fb62b89f4916b721be55ceb828dbd73": {
        "privateKey": "8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63",
        "comment": "private key and this comment are ignored.  In a real chain, the private key should NOT be stored",
        "balance": "0xad78ebc5ac6200000"
      },
      "627306090abaB3A6e1400e9345bC60c78a8BEf57": {
        "privateKey": "c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3",
        "comment": "private key and this comment are ignored.  In a real chain, the private key should NOT be stored",
        "balance": "90000000000000000000000"
      },
      "f17f52151EbEF6C7334FAD080c5704D77216b732": {
        "privateKey": "ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f",
        "comment": "private key and this comment are ignored.  In a real chain, the private key should NOT be stored",
        "balance": "90000000000000000000000"
      }
    }
  },
}
```

Salve o arquivo.

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
python3 -m json.tool "$CONFIGDIR/besu/genesis.json"
```
## 4.4 Criar o arquivo static-nodes.json de cada nó


### Passo 1: Gerar o arquivo base-static-nodes.json lendo as chaves públicas

Inicialize o array JSON e popule iterativamente com as chaves reais de cada nó (`key.pub`) e seus respectivos IPs estáticos:

```bash
echo "[]" > "$NODESDIR/base-static-nodes.json"

for i in {1..3}; do
  for j in {1..2}; do
    NODE="org${i}-node${j}"
    IP="172.20.0.${i}${j}"
    PUBKEY=$(cat "$NODESDIR/$NODE/key.pub" | tr -d '\r\n ' | sed 's/^0x//')
    jq --arg enode "enode://${PUBKEY}@${IP}:30303" '. += [$enode]' \
      "$NODESDIR/base-static-nodes.json" > tmp.json && mv tmp.json "$NODESDIR/base-static-nodes.json"
  done
done

```

### Passo 2: Distribuir o static-nodes.json de cada nó excluindo o seu próprio IP

Cada nó deve conhecer apenas os outros 5 nós da rede:

```bash
for i in {1..3}; do
  for j in {1..2}; do
    NODE="org${i}-node${j}"
    IP="172.20.0.${i}${j}"
    jq --arg ip "$IP" '[.[] | select(contains($ip) | not)]' \
      "$NODESDIR/base-static-nodes.json" > "$NODESDIR/$NODE/static-nodes.json"
  done
done

```

---

## 4.5 Validar os arquivos de configuração

Confira se cada pasta gerou um array JSON válido contendo exatamente 5 nós:

```bash
for d in "$NODESDIR"/org*-node*; do
  echo -n "$(basename "$d"): "
  jq '. | length' "$d/static-nodes.json"
done

```

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
python3 -m json.tool "$NODESDIR/org1-node1/static-nodes.json"

```

---

## 5. Gerar os arquivos Docker Compose dos nodes

### 5.1 Criar os diretórios de dados

Crie os diretórios de persistência isolados para todas as organizações:

```bash
for i in {1..3}; do
  for j in {1..2}; do
    mkdir -p "$BASEDIR/volumes/org${i}-node${j}"
  done
done

```

### 5.2 Criar a rede Docker compartilhada com subnet estático

Crie a rede bridge definindo o bloco CIDR compatível com a tabela de enodes:

```bash
docker network create --subnet=172.20.0.0/16 rede-besu-net

```

---

### 5.3 Criar o arquivo Docker Compose da Org1

Crie o arquivo:

```bash
nano "$BASEDIR/docker-compose-org1.yml"

```

Insira a configuração com os IPs fixos e parâmetros P2P ajustados:

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
      - --p2p-host=172.20.0.11
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
      besu-net:
        ipv4_address: 172.20.0.11

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
      - --p2p-host=172.20.0.12
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
      besu-net:
        ipv4_address: 172.20.0.12

networks:
  besu-net:
    external: true
    name: rede-besu-net

```

---

### 5.4 Gerar automaticamente os arquivos da Org2 e Org3

Execute os comandos a seguir para replicar o compose e aplicar as substituições de nomes, portas expostas no host e endereços IP de cada nó:

#### Configurar Org2:

```bash
cp "$BASEDIR/docker-compose-org1.yml" "$BASEDIR/docker-compose-org2.yml"

# Substitui referências de nomes e caminhos
sed -i 's/org1/org2/g' "$BASEDIR/docker-compose-org2.yml"

# Atualiza portas expostas no host (P2P: 30303/30304 | RPC: 8547/8548 | Métricas: 9547/9548)
sed -i 's/30301:30303/30303:30303/g' "$BASEDIR/docker-compose-org2.yml"
sed -i 's/30302:30303/30304:30303/g' "$BASEDIR/docker-compose-org2.yml"
sed -i 's/8545:8545/8547:8545/g'     "$BASEDIR/docker-compose-org2.yml"
sed -i 's/8546:8545/8548:8545/g'     "$BASEDIR/docker-compose-org2.yml"
sed -i 's/9545:9545/9547:9545/g'     "$BASEDIR/docker-compose-org2.yml"
sed -i 's/9546:9545/9548:9545/g'     "$BASEDIR/docker-compose-org2.yml"

# Atualiza IPs estáticos para Org 2
sed -i 's/172\.20\.0\.11/172.20.0.21/g' "$BASEDIR/docker-compose-org2.yml"
sed -i 's/172\.20\.0\.12/172.20.0.22/g' "$BASEDIR/docker-compose-org2.yml"

```

#### Configurar Org3:

```bash
cp "$BASEDIR/docker-compose-org1.yml" "$BASEDIR/docker-compose-org3.yml"

# Substitui referências de nomes e caminhos
sed -i 's/org1/org3/g' "$BASEDIR/docker-compose-org3.yml"

# Atualiza portas expostas no host (P2P: 30305/30306 | RPC: 8549/8550 | Métricas: 9549/9550)
sed -i 's/30301:30303/30305:30303/g' "$BASEDIR/docker-compose-org3.yml"
sed -i 's/30302:30303/30306:30303/g' "$BASEDIR/docker-compose-org3.yml"
sed -i 's/8545:8545/8549:8545/g'     "$BASEDIR/docker-compose-org3.yml"
sed -i 's/8546:8545/8550:8545/g'     "$BASEDIR/docker-compose-org3.yml"
sed -i 's/9545:9545/9549:9545/g'     "$BASEDIR/docker-compose-org3.yml"
sed -i 's/9546:9545/9550:9545/g'     "$BASEDIR/docker-compose-org3.yml"

# Atualiza IPs estáticos para Org 3
sed -i 's/172\.20\.0\.11/172.20.0.31/g' "$BASEDIR/docker-compose-org3.yml"
sed -i 's/172\.20\.0\.12/172.20.0.32/g' "$BASEDIR/docker-compose-org3.yml"

```

---

## 5.5 Validar as configurações

> [!IMPORTANT]
> <span style="color:purple;"><strong>EXECUTE A VALIDAÇÃO ABAIXO</strong></span>:

```bash
docker compose -f "$BASEDIR/docker-compose-org1.yml" config > /dev/null && echo "Org1 OK"
docker compose -f "$BASEDIR/docker-compose-org2.yml" config > /dev/null && echo "Org2 OK"
docker compose -f "$BASEDIR/docker-compose-org3.yml" config > /dev/null && echo "Org3 OK"

```

Confira o alinhamento de portas e IPs atribuídos:

```bash
grep -E 'p2p-host|ipv4_address' "$BASEDIR"/docker-compose-org*.yml

```

---

## 5.6 Iniciar todos os nós da rede

Suba as três organizações:

```bash
docker compose -p org1 -f "$BASEDIR/docker-compose-org1.yml" up -d
docker compose -p org2 -f "$BASEDIR/docker-compose-org2.yml" up -d
docker compose -p org3 -f "$BASEDIR/docker-compose-org3.yml" up -d

```

Verifique o status de todos os contêineres:

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

```

---

## 5.7 Validar a conectividade e consenso

Consulte os peers conectados ao primeiro nó (a resposta `"0x5"` indica 5 peers conectados):

```bash
curl -s http://127.0.0.1:8545 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}' | jq .

```

Confirme a produção de blocos via QBFT:

```bash
curl -s http://127.0.0.1:8545 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```
Modifique o tipo de nível de log:
```bash
curl -s http://127.0.0.1:8545 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","method":"admin_changeLogLevel","params":["DEBUG"],"id":1}'
```

Para mais comandos, consulte a [documentação](https://docs.besu-eth.org/public-networks/reference/api).

