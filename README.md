# Set up 3 node zookeeper-kafka cluster in docker swarm
# Setup
Before starting the cluster you will need to install docker (docker-engine >= 0.10.0) and docker-compose. If you already have these installed, you can skip docker installation
# Install docker on all the three nodes (node1, node2 and node3)
# Setting up Docker on Ubuntu
You can install docker from here https://docs.docker.com/engine/install/ubuntu/
# Setting up Docker on Centos
You can install docker from here https://docs.docker.com/engine/install/centos/

# Configure 3 node docker swarm cluster where one node will be leader and the other two are follower
Run command on node1 to initialize docker swarm cluster and save the token
sudo docker swarm init


Once you have docker installed, install docker-compose here https://docs.docker.com/compose/install/


