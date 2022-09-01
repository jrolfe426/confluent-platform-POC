# Confluent Platform POC
## 4 node environment using VMs to deploy a CP environment


### Background:
This is a walkthrough on how to successfully deploy and configure a complete Confluent Platform environment on 4 separate VMs.   This scenario was brought on by a customer that wanted to test CP in their environment but did not want to provision 10 VMs as suggested in the Confluent Documentation.  This example is based on the [Confluent Documentation](https://docs.confluent.io/platform/current/installation/installing_cp/deb-ubuntu.html).
The following environment is not a best practice for production or staging as there is only a single broker and a single zookeeper.  This is merely an exercise that will help a customer evaluate CP with minimal nodes and 0 redundancy on virtual machines.

#### Pre-reqs
4 GKE Compute Engine instances
  - Debian 10 e2-standard-2 (2)  
  - Debian 10 e2-medium (2)
  - VMs to all belong to a common VPC/Subnet

This tutorial deploys the VMs on GKE VMs, Debian 10, us-central1-c, with the following VM/Service breakout:

| VM Name                 | Services           |      VM Size  | Internal IP | External IP |
| ----------------------- |:------------------:| -------------:| -----------:| -----------:|
| jr-zookeeper-broker1    | zookeeper, broker1 | e2-standard-2 | 10.1.0.3   | 35.xxx.xxx.xxx
| jr-ksqldb              | ksqldb             |   e2-medium   | 10.1.0.6 | 35.xxx.xxx.xxx |
| jr-connect-sr-restproxy | connect, schema registry, restproxy|    e2-medium  | 10.1.0.9 | 35.xxx.xxx.xxx |
| jr-controlcenter        | control center     | e2-standard-2 | 10.1.0.10 | 35.xxx.xxx.xxx |

Networking - All VMs are in the same VPC/subnet.  If the preferred access method is to use a local machine to access the CP VMs, a firewall rule will need to be allowed on port 22/SSH.  This rule should be added at the subnet.  Suggested (easy) method is to connect to the VM directly from the google console.

### VM Setup
In order to deploy Confluent on the Linux Debian 10 VMs, there is a series of initial steps that must be followed on each Virtual Machine:

Each of the following commands to be run from the linux shell - 

1.  sudo apt-get update
2.  sudo apt-get upgrade
3.  sudo apt-get install wget
4.  sudo apt-get -y install gnupg
5.  sudo wget -qO - https://packages.confluent.io/deb/7.2/archive.key | sudo apt-key add -
6.  sudo apt-get install software-properties-common
7.  sudo apt-get update
8.  sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/7.2 stable main"
    sudo add-apt-repository "deb https://packages.confluent.io/clients/deb $(lsb_release -cs) main"9.  sudo apt-get update && sudo apt-get install   confluent-platform
9.  sudo apt-get update && sudo apt-get install confluent-platform
10.  sudo apt-get update
11.  sudo apt install default-jre
