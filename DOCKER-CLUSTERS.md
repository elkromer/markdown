---
tags:
	- Docker
	- Swarm
	- Kubernetes
	- Clusters
---

# Docker: Cluster Management
> How do we deploy and maintain dozens, hundreds, or thousands of containers across many servers at a time? Docker Swarm Mode provides cluster management inside docker. Kubernetes 

## Background

### Kubernetes



### Docker Swarm Mode

A server clustering solution built inside Docker. A major evolution in the scope of problems docker was designed to solve. It used to be an add-on component that didn't help at a large scale but that changed in 2016, when it was added into docker daemon. It isn't enabled out of the box. 

# Swarm Overview

When creating a service you define an optimal state and Docker works to maintain that desired state. 

A swarm consists of multiple docker hosts which run in **swarm mode** and act as managers and workers. A "host" is an instance of the Docker engine participating in the swarm and would run on a virutal machine or physical machine. A given host can be a manager, a worker, or both. That sounds a little weird to me... Think of it this way: in most cases, a host acting as a manager also runs services as a worker. The definition of `swarm` is just wide enough to let people know that managers and workers can be decoupled across machines

A **node** is a general term for a manager or worker. If some worker node goes down, Docker schedules that node's tasks on other nodes (a `task` launches a container). 

![Swarm Nodes](/resources/docker-swarm-nodes.png)

The blue boxes are manager nodes. Manager nodes dispatch units of work called tasks to worker nodes. They perform the orchestration and cluster management functions required to maintain the desired state of the swarm. 

Each of them have a database locally on them, called the `Raft` database, that stores the configuration and gives them the information they need in order to be considered the authority inside a swarm. Traffic is encrypted for integrity and to have a guarantee that a manager node is trusted to administer workers in a swarm.
>By default, manager nodes also run services as worker nodes, but you can configure them to run manager tasks exclusively and be manager-only nodes. 

Worker nodes receive and execute tasks dispatched from manager nodes. It also notifies the manager nodes of the current state of its assigned tasks so that the manager can maintain the desired state of each worker.

## Swarm Commands

Keep in mind, in cluster management you don't really care so much about individual nodes or even name them. You treat them like cattle. There is just a number of nodes and it doesn't make sense to go into each node and start up an individual container. We really just throw requirements at the swarm in the form of services and it will orchestrate how to lay that out. We just let it handle that and know it has got our back.

### docker swarm init

Does lots of PKI and security automation. Initializes the Raft database. Configures the Root Signing Certificate and issues a certificate for the first Manager node. Creates join tokens and prints out the join command.

The `swarm` command is really just used to initialize, join, leave.

### docker node

The main node management command. There can only be one `Leader` manager node at a time. With the `docker node` command you can bring nodes in and out of the swarm and demote or promote them.

### docker service

Service in a swarm replaces the `docker run`. The main service management command.

## Overlay Multi-Host Networking

When creating a network like you are used to creating it, you can specify a special network driver called `overly`, which is like specifying a swarm-wide bridge network. The containers on other hosts can access each other like they are on the same VLAN. 

This is only for intra-swarm communication. The overlay driver doesn't play a huge role in the traffic coming into the network. Conatiners on the same overlay network are accessible by their container name. 

You can create a logical separation between the frontend and backend by attaching a container to two overlay networks. E.g. A middleman between the website and database. 

## Routing Mesh

Routes ingress (incoming) packets for a Service to the proper Task. Spans all nodes in a Swarm. The routing mesh also load balances Swarm Services across their Tasks. 

**Internal representation**:

![](/resources/docker-routing-vip.png)

When `docker service create` executes, a virtual IP (VIP) is created based on the DNS name of the service in the overlay network. Any other containers in the overlay network `my-network` that need to talk to the service can do so using the VIP. The VIP will properly load balance to all the tasks in the service.

All of the above holds true for internal traffic among containers in the overlay network. When talking about traffic coming in from the outside world, the routing mesh has different behavior.

You can see the routing mesh work by running a quick test: Lets say you have 1 replica of a webserver running on one node in your three-node swarm. Then you can enter the IP address of *any* node on the swarm and be directed to the particular node that is running the website.

![Routing Mesh](/resources/docker-routing-mesh.png)

**All nodes listen on published ports for external traffic** and the swarm load balancer reroutes the traffic to the appropriate node based on the load balancing rules.