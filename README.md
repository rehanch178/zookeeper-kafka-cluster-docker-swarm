# Set up 3 node zookeeper-kafka cluster in docker swarm
# Setup
Before starting the cluster you will need to install docker (docker-engine >= 0.10.0) and docker-compose. If you already have these installed, you can skip docker installation
# Install docker on all the three nodes (node1, node2 and node3)
# Setting up Docker on Ubuntu
You can install docker from here https://docs.docker.com/engine/install/ubuntu/
# Setting up Docker on Centos
You can install docker from here https://docs.docker.com/engine/install/centos/

# Configure 3 node docker swarm cluster where one node will be leader and the other two are worker
Run command on node1 to initialize docker swarm cluster and save the token appers on the console. This token will be required to configure worker nodes

sudo docker swarm init

Execute the docker swarm join command on the other two nodes ( node2 and node3). After successful join to swarm leader node, run command to verify the cluster

sudo docker node ls

    ID                            HOSTNAME     STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
    4w7axt8g69fu625s9bwmdu99p     node2        Ready     Active                          20.10.7
    kebjh0g9wtzh7yvghnrglvh8r *   node1        Ready     Active         Leader           20.10.7
    n7chbyltg638d96gg624r8psq     node3        Ready     Active                          20.10.7




Once you have docker installed, install docker-compose here https://docs.docker.com/compose/install/


