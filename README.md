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
- KSQLDB Deployment
  - kSQLDB
  1. Log into designated VM hosting ksqldb and navigate to /etc/ksqldb/ksql-production-server.properties
     - sudo vi ksql-production-server.properties
     - edit the file to resemble the example below.
      <img width="879" alt="Screen Shot 2022-09-06 at 10 13 33 AM" src="https://user-images.githubusercontent.com/100879140/188671840-492b11b1-706b-499b-9974-cdfd2d8583de.png">
      <img width="881" alt="Screen Shot 2022-09-06 at 10 14 26 AM" src="https://user-images.githubusercontent.com/100879140/188672046-da9f0e31-4673-4e74-a5d4-6fbec0ac1cd0.png">
  2. Once the properties file is updated and saved you can start the services:
     - ksqldb:  sudo systemctrl start confluent-ksqldb
  3. Check the status of the service:
     - systemctl status confluent*
     - Below is a screenshot of a successful run
       <img width="887" alt="Screen Shot 2022-09-06 at 10 17 56 AM" src="https://user-images.githubusercontent.com/100879140/188672774-a07b42c0-e6ae-475e-b0f9-61aa98c70fbc.png">
- Connect, Schema Registry, Rest Proxy Deployment
  - Connect
  1. Log into designated VM hosting Connect and navigate to /etc/kafka/connect-distributed.properties
     - sudo vi connect-distributed.properties
     - edit the file to resemble the example below:
        <img width="883" alt="Screen Shot 2022-09-06 at 10 34 47 AM" src="https://user-images.githubusercontent.com/100879140/188676536-744a62fd-b459-4799-b1fa-e997482bff48.png">
        <img width="885" alt="Screen Shot 2022-09-06 at 10 35 44 AM" src="https://user-images.githubusercontent.com/100879140/188676729-6a9a34e9-e7fa-46d8-a773-b40b39cc63a2.png">
  - Schema Registry
  1. Log into designated VM hosting Schema Registry and navigate to /etc/schema-registry/schema-registry.properties
     - sudo vi schema-registry.properties
     - edit the file to resemble the example below:
       <img width="889" alt="Screen Shot 2022-09-06 at 10 40 33 AM" src="https://user-images.githubusercontent.com/100879140/188677842-00bb0b24-29af-4c65-9304-715fb67f55af.png">
   - Rest Proxy
  1.  Log into designated VM hosting Rest Proxy and navigate to /etc/kafka-rest/kafka-rest.properties
      - sudo vi kafka-rest.propertes
      - edit the file to resemble the example below:
        <img width="881" alt="Screen Shot 2022-09-06 at 10 50 35 AM" src="https://user-images.githubusercontent.com/100879140/188680063-8c3ae73e-48d5-4788-b1df-fb3c08081d56.png">
  2. Once both properties files are updated and saved you can start the services:
     - Connect:  sudo systemctrl start confluent-kafka-connect
     - Schema Registry: sudo systemctl start confluent-schema-registry
     - Rest Proxy: sudo systemctl start confluent-kafka-rest

  3. Check the status of the three services:
     - systemctl status confluent*
     - Below is a screenshot of a successful run
        <img width="886" alt="Screen Shot 2022-09-06 at 10 55 39 AM" src="https://user-images.githubusercontent.com/100879140/188681187-a2fdc652-1687-465d-bd5d-b93ad5ac1076.png">
- Confluent Control Center
  - Control Center
  1. Log into designated VM hosting Control Center and navigate to /etc/confluent-control-center
     - sudo vi control-center-production.properties
     - edit the file to resemble the example below:
      <img width="1045" alt="Screen Shot 2022-09-06 at 11 09 18 AM" src="https://user-images.githubusercontent.com/100879140/188684081-fe1c5034-09bf-4b4c-a768-9285f2727b0b.png">
      <img width="929" alt="Screen Shot 2022-09-06 at 11 10 13 AM" src="https://user-images.githubusercontent.com/100879140/188684255-08d287b9-d371-4212-b4a4-9851cafd2fcc.png">
      <img width="893" alt="Screen Shot 2022-09-02 at 5 22 34 PM" src="https://user-images.githubusercontent.com/100879140/188684817-b5ab913e-a336-4a48-a0a4-053c3fe54ad9.png">
  2. Once the properties file is updated and saved you can start the service:
     - Control Center:  sudo systemctrl start confluent-control-center
  3. Check the status of service:
     - systemctl status confluent*
     - Below is a screenshot of a successful run
        <img width="1373" alt="Screen Shot 2022-09-06 at 11 18 00 AM" src="https://user-images.githubusercontent.com/100879140/188685734-2e988428-9196-46fa-8e9a-b37057904ff4.png">
  4. Log into the Control Center UI by accessing the public IP address assigned to the Control Center VM - "xxx.xxx.xxx.xxx:9021".  Ensure that the cluster is healthy and that the ksqlDB and connect clusters are attached.

        <img width="850" alt="Screen Shot 2022-09-06 at 11 27 43 AM" src="https://user-images.githubusercontent.com/100879140/188687659-76936696-899c-462d-8cbe-89b6a0f0fe86.png">
