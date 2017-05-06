# fabric-logging-efk
Fabric docker loggin via EFK (Elasticsearch + Fluentd + Kibana) Stack

This article explains how to collect Hyperledge Fabric logs to EFK (Elasticsearch + Fluentd + Kibana) stack. The example uses Docker Compose for setting up multiple containers.

### Table of Contents
Prerequisites: Docker
1. prepare docker-compose.yml
1. Start EFK containers
1. Start Fabric Containers
1. Access Kibana
1. Config index from Kibana

### Prepare docker-compose file and work dir
```
git clone https://github.com/machozhao/fabric-logging-efk
cd fabric-logging-efk
```

### Start EFK services
```
docker-compose -f docker-conpose-efk.yaml up
```
### Check EFK services
```
# Access Kibana WebUI with Chrome browser or other HTML5 brwoser.
chrome http://localhost:5601
```

### Add log driver for your Fabric containers:
#### Docker Compose
```
    links:
      - fluentd
    depends_on:
      - fluentd
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        fluentd-address: localhost:24224
        tag: httpd.access
```        
#### Or Docker
```
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        fluentd-address: localhost:24224
        tag: httpd.access
```        
#### Or set up default Docker container log driver for all of containers.
```
```
### For example, when using fabric 1.0.0 release.tgz, these steps for your references:
#### Change base peer docker compose file: e2e/peer-base/peer-base-no-tls.yaml 
```
# Add the following content 
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        fluentd-address: localhost:24224
        tag: fabric.peer
```
#### Change orderer definition in main compose file: e2e/docker-compose-no-tls.yaml 
```
# Add the following content for orderer 
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        fluentd-address: localhost:24224
        tag: fabric.orderer
```
#### Change cli definition in main compose file: e2e/docker-compose-no-tls.yaml 
```
# Add the following content for orderer 
    logging:
      driver: "fluentd"
      options:
        fluentd-async-connect: "true"
        fluentd-address: localhost:24224
        tag: fabric.cli
```

### Start Hyperledge Fabric 1.0.0-alpha sample services
```
# this sets our OS
export ARCH_TAG=$(uname -m)
# this starts the network in "detached" mode; enter the appropriate value for the CHANNEL_NAME parm
CHANNEL_NAME=<CHANNEL_NAME> docker-compose -f docker-compose-no-tls.yaml up -d
CHANNEL_NAME=<CHANNEL_NAME> docker-compose -f docker-compose-no-tls.yaml up
```
More detail please reference Fabric online document: http://hyperledger-fabric.readthedocs.io/en/latest/getting_started.html


### Start and Access
```bash
docker-compose -f docker-compose-efk.yaml up
http://localhost:5601
```
### Create and set default index for Kibana: 
```
fluentd-*
```
### Generate TX and view logs in Kibana
You can do transaction in Fabric, and we will find related logs in Kibana:
#### Invoke TX
```
docker exec -it cli bash
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com CORE_PEER_ADDRESS=peer0.org1.example.com:7051 CORE_PEER_LOCALMSPID="Org0MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/cacerts/org1.example.com-cert.pem peer chaincode invoke -o orderer.example.com:7050 -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'
```
#### View logs in Kibana
!["Kibana Scree Shopt"](https://github.com/machozhao/fabric-logging-efk/raw/master/docs/images/kibana_screen_shot_1.png)
*Fig. 1: Kibana Scree Shopt*

### References
* https://docs.docker.com/engine/admin/logging/overview/
* http://www.imekaku.com/2016/09/26/fluentd-conclusion/
