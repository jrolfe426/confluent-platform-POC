# confluent-platform-POC
4 node environment using VMs to deploy a CP environment



This is a walkthrough on how to successfully deploy and configure a complete Confluent Platform environment on 4 separate VMs.   The scenario that sparked this project is that a customer wanted to test CP in their environment but did not want to provision 10 VMs as suggested by the Confluent Documentation.  The following environment is not a best practice as there is only a single broker and a single zookeeper.  This is merely an exercise that will help a customer evaluate CP with minimal nodes and 0 redundancy.  

This tutorial deploys the VMs on GKE VMs, Debian 10 with the following VM/Service breakout:

 
 jr-zookeeper-broker1 - Zookeeper, Broker
 jr-connect-sr-restproxy         Connect, Schema Registry, Rest Proxy
 jr-ksqldb                       kSQLDB
 jr-control-center               Confluent Control Center (c3)
