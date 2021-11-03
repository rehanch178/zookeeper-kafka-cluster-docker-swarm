# Set up 3 node zookeeper-kafka cluster in docker swarm (DRAFT)
# Setup
Before starting the cluster you will need to install docker (docker-engine >= 0.10.0) and docker-compose. If you already have these installed, you can skip docker installation
# Install docker on all the three nodes (node1, node2 and node3)
# Setting up Docker
Ubuntu > You can install docker from here https://docs.docker.com/engine/install/ubuntu/

Centos > You can install docker from here https://docs.docker.com/engine/install/centos/

# Configure 3 node docker swarm cluster where one node will be leader and the other two are worker
Run command on node1 to initialize docker swarm cluster and save the token appers on the console. This token will be required to configure worker nodes

    sudo docker swarm init

Execute the docker swarm join command on the other two nodes ( node2 and node3). After successful join to swarm leader node, run command to verify the cluster

    sudo docker node ls

    ID                            HOSTNAME     STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
    4w7axt8g69fu625s9bwmdu99p     node2        Ready     Active                          20.10.7
    kebjh0g9wtzh7yvghnrglvh8r *   node1        Ready     Active         Leader           20.10.7
    n7chbyltg638d96gg624r8psq     node3        Ready     Active                          20.10.7

Add docker labels on the nodes. Labels will be rquired by the compose files to deploy zookeeper and kafka containers on each node.
Run docker label commands from the leader node. Her leader node is node1, docker swarn commands should be run from the leader node only.
    
    sudo docker node update --label-add  node=node1 <NODE_ID> (Get node ID from docker node ls output and pass) 
    sudo docker node update --label-add  node=node2 <NODE_ID> (Get node ID from docker node ls output and pass)
    sudo docker node update --label-add  node=node3 <NODE_ID> (Get node ID from docker node ls output and pass)
    
Create overlay network which is required for internal communication of the containers in the docker swarm cluster

    docker network create -d overlay --attachable vnetwork

Create zookeeper/kafka data and log directories for data and log persistenet on the host machine.The directories will be used to mount host vloume to the docker container and when data is written on containers data dorectores, data will be stored into the specified host path. Run commands on all the 3 nodes.

    sudo mkdir -p /data/zookeeper
    sudo mkdir -p /data/log/zookeeper
    sudo mkdir -p /data/kafka
    sudo mkdir -p /data/log/kafka
    
Label all the nodes to deploy zookeeper services in all the 3 nodes based on node label.

Get zookeeper docker-compose file from here https://github.com/rehanch178/zookeeper/blob/main/zookeeper-docker-compose.yaml , save it in a file and run command to setup 3 node zookeeper cluster as docker swarm stack

    sudo docker stack deploy -c zookeeper-docker-compose.yaml zk

Once docker stack is deployed and all the services are up then verify zookeeper cluster has form the Quorum where one zookeeper node will be leader and the other two are follower. Login to each node's zookeeper container, run command and verify Mode of the command output.

    [root@zookeeper2 /]# echo stat | nc localhost 2181
    Zookeeper version: 3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
    Clients:
      /127.0.0.1:49066[0](queued=0,recved=1,sent=0)

    Latency min/avg/max: 0/0.0/0
    Received: 2
    Sent: 1
    Connections: 1
    Outstanding: 0
    Zxid: 0x1800000000
    Mode: leader
    Node count: 25
    Proposal sizes last/min/max: -1/-1/-
    
The other node's mode should be Mode: follower

Now setup Kafka cluster.Get kafka docker-compose file from here https://github.com/rehanch178/kafka/blob/main/kafka-docker-compose.yaml, save it in a file with name docker-compose.yaml and run command to setup 3 node kafka cluster as docker swarm stack

    sudo docker stack deploy -c kafka-docker-compose.yaml kafka
    
Once docker stack is deployed and all the services are up then verify each kafka node has joined zookeeper cluster as kakfa brokers. Login to one of the zookeeper container, run command and list kafka brokers. The output should be [1, 2, 3] where 1, 2 and 3 are broker id's.

    [root@zookeeper2 /]# ./zookeeper/bin/zkCli.sh ls /brokers/ids
    /usr/bin/java
    Connecting to localhost:2181

    WATCHER::

    WatchedEvent state:SyncConnected type:None path:null
    [1, 2, 3]
    
Login to one of the kafka container using docker exec <container_id> bash
# Create Kafka Topics
    ./kafka/bin/kafka-topics.sh --create --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --topic TutorialTopic
# List kafka Topics
    ./kafka/bin/kafka-topics.sh --list --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092
# Describe Kafka Topic
    ./kafka/bin/kafka-topics.sh --describe --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --topic TutorialTopic
# Push Message to Kafka Topic
    echo "Hello, World" | ./kafka/bin/kafka-console-producer.sh --broker-list kafka1:9092,kafka2:9092,kafka3:9092 --topic TutorialTopic
# Cosume Message from Kafka Topic
    ./kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --topic TutorialTopic --from-beginning

    
    

