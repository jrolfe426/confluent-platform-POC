# confluent-platform-POC
4 node environment using VMs to deploy a CP environment



This is a walkthrough on how to successfully deploy and configure a complete Confluent Platform environment on 4 separate VMs.   This tutorial deploys the VMs on GKE VMs, Debian 10 with the following VM/Service breakout:

 **Server Name**                 **Kafka Services**                        **VM Size**
 jr-zookeeper-broker1            Zookeeper, Broker
 jr-connect-sr-restproxy         Connect, Schema Registry, Rest Proxy
 jr-ksqldb                       kSQLDB
 jr-control-center               Confluent Control Center (c3)
