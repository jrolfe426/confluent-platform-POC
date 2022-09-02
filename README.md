# Confluent Platform POC
## 4 node environment using VMs to deploy a CP environment


### Background:
This is a walkthrough on how to successfully deploy and configure a complete Confluent Platform environment on 4 separate VMs.   This scenario was brought on by a customer that wanted to test CP in their environment but did not want to provision 10 VMs as suggested in the Confluent Documentation.  This example is based on the [Confluent Documentation](https://docs.confluent.io/platform/current/installation/installing_cp/deb-ubuntu.html).
The following environment is not a best practice for production or staging as there is only a single broker and a single zookeeper.  This is merely an exercise that will help a customer evaluate CP with minimal nodes and 0 redundancy on virtual machines.

#### Prerequisites
4 GKE Compute Engine instances
  - Debian 10 e2-standard-2 (2)  
  - Debian 10 e2-medium (2)
  - VMs to all belong to a common VPC/Subnet
  - Basic admin experience in GCP
  - Basic text editing experience e.g VIM

This tutorial deploys the VMs on GKE VMs, Debian 10, us-central1-c, with the following VM/Service breakout:

| VM Name                 | Services           |      VM Size  | Internal IP | External IP |
| ----------------------- |:------------------:| -------------:| -----------:| -----------:|
| jr-zookeeper-broker1    | zookeeper, broker1 | e2-standard-2 | 10.1.0.3   | 
| jr-ksqldb              | ksqldb             |   e2-medium   | 10.1.0.6 |  |
| jr-connect-sr-restproxy | connect, schema registry, restproxy|    e2-medium  | 10.1.0.9 |  |
| jr-controlcenter        | control center     | e2-standard-2 | 10.1.0.10 |  |

Networking - All VMs are in the same VPC/subnet.  If the preferred access method is to use a local machine to access the CP VMs, a firewall rule will need to be allowed on port 22/SSH.  This rule should be added at the subnet.  Suggested (easy) method is to connect to the VM directly from the google console.

### VM Setup
In order to deploy Confluent on the Linux Debian 10 VMs, there is a series of initial steps that must be followed on each Virtual Machine:

Run each of the following command from the linux shell - 

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

## CP Configuration:
- Zookeeper-Broker Deployment
  - Zookeeper
  1. Log into designated VM hosting zookeeper/broker and navigate to /etc/kafka/zookeeper.properties
     - sudo vi zookeeper.properties
     - edit the file to resemble the example below. Ensure to add your VM's internal IP address
     <img width="774" alt="Screen Shot 2022-09-02 at 1 27 39 PM" src="https://user-images.githubusercontent.com/100879140/188215617-98d51da2-3944-4726-80ff-2a8454fcc39d.png">
  - Broker Deployment
  1. Log into designated VM hosting zookeeper/broker and navigate to /etc/kafka/server.properties
     - sudo vi server.properties
     - edit the file to resemble the example below.  
    

     <img width="884" alt="Screen Shot 2022-09-02 at 1 36 08 PM" src="https://user-images.githubusercontent.com/100879140/188216851-d65e3b60-60ae-4d4c-9de6-3548a120edc9.png">

     
<img width="1029" alt="Screen Shot 2022-09-02 at 1 39 17 PM" src="https://user-images.githubusercontent.com/100879140/188217313-3f084360-7d93-427c-8d8f-24252aca811c.png">
<img width="1033" alt="Screen Shot 2022-09-02 at 1 39 52 PM" src="https://user-images.githubusercontent.com/100879140/188217406-cee0296f-0342-489e-b6ff-b9578d557b06.png">
<img width="922" alt="Screen Shot 2022-09-02 at 1 41 02 PM" src="https://user-images.githubusercontent.com/100879140/188217552-bdcf31ec-e9a1-4a1d-9fc2-d807c050e9d1.png">

  2. Once both properties files are updated and saved you can start the services:
     - Zookeeper:  systemctrl start confluent-zookeeper
     - Broker: systemctl start confluent-server

  3. Check the status of both services:
     - systemctl status confluent*
     - Below is a screenshot of a successful run
<img width="1029" alt="Screen Shot 2022-09-02 at 1 50 28 PM" src="https://user-images.githubusercontent.com/100879140/188218807-4893a970-fac2-4268-b191-c261acd3f804.png">
