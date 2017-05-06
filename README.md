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

### Add log driver for your container:
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

### Reference
https://docs.docker.com/engine/admin/logging/overview/
