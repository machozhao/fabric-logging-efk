# fabric-logging-efk
Fabric docker loggin via EFK (Elasticsearch + Fluentd + Kibana) Stack

This article explains how to collect Hyperledge Fabric logs to EFK (Elasticsearch + Fluentd + Kibana) stack. The example uses Docker Compose for setting up multiple containers.

Table of Contents
Prerequisites: Docker
Step 0: prepare docker-compose.yml
Step 1: Prepare Fluentd image with your Config + Plugin
Step 2: Start Containers
Step 3: Generate httpd Access Logs
Step 4: Confirm Logs from Kibana

