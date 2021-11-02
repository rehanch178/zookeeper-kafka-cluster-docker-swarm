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




