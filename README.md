# Confluent Platform POC
## 4 node environment using VMs to deploy a CP environment


### Background:
This is a walkthrough on how to successfully deploy and configure a complete Confluent Platform environment on 4 separate VMs.   This scenario was brought on by a customer that wanted to test CP in their environment but did not want to provision 10 VMs as suggested in the Confluent Documentation.  This example is based on the [Confluent Documentation](https://docs.confluent.io/platform/current/installation/installing_cp/deb-ubuntu.html).
The following environment is not a best practice as there is only a single broker and a single zookeeper.  This is merely an exercise that will help a customer evaluate CP with minimal nodes and 0 redundancy on virtual machines.  

This tutorial deploys the VMs on GKE VMs, Debian 10, us-central1-c, with the following VM/Service breakout:

| VM Name                 | Services           |      VM Size  | Internal IP | External IP |
| ----------------------- |:------------------:| -------------:| -----------:| -----------:|
| jr-zookeeper-broker1    | zookeeper, broker1 | e2-standard-2 | 10.1.0.3   | 35.xxx.xxx.xxx
| jr-ksqldb               | ksqldb             |   e2-medium   | 10.1.0.6 | 35.xxx.xxx.xxx |
| jr-connect-sr-restproxy | connect, schema registry, restproxy|    e2-medium  | 10.1.0.9 | 35.xxx.xxx.xxx |
| jr-controlcenter        | control center     | e2-standard-2 | 10.1.0.10 | 35.xxx.xxx.xxx |

Networking - All VMs are in the same VPC/subnet.  Each VM will need a firewall rule to allow port 22/SSH.  This rule should be added at the subnet.

