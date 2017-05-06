# fabric-logging-efk
Fabric docker loggin via EFK (Elasticsearch + Fluentd + Kibana) Stack

This article explains how to collect Hyperledge Fabric logs to EFK (Elasticsearch + Fluentd + Kibana) stack. The example uses Docker Compose for setting up multiple containers.

### Table of Contents
Prerequisites: Docker
1. prepare docker-compose.yml
1. Prepare Fluentd image with your Config + Plugin
1. Start Containers
1. Generate httpd Access Logs
1. Confirm Logs from Kibana

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

### References
* https://docs.docker.com/engine/admin/logging/overview/
* http://www.imekaku.com/2016/09/26/fluentd-conclusion/
