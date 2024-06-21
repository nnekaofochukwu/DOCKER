Docker Networking Overview

Container networking refers to the ability for containers to connect to and communicate with each other, or to non-Docker workloads.

Containers have networking enabled by default, and they can make outgoing connections. 
A container has no information about what kind of network it's attached to, or whether their peers are also Docker workloads or not. 
A container only sees a network interface with an IP address, a gateway, a routing table, DNS services, and other networking details. 
That is, unless the container uses the none network driver.


Bridge network driver
In terms of networking, a bridge network is a Link Layer device which forwards traffic between network segments. 
A bridge can be a hardware device or a software device running within a host machine's kernel.

In terms of Docker, a bridge network uses a software bridge which lets containers connected to the same bridge network communicate, 
while providing isolation from containers that aren't connected to that bridge network. 
The Docker bridge driver automatically installs rules in the host machine so that containers on different bridge 
networks can't communicate directly with each other.

Bridge networks apply to containers running on the same Docker daemon host. 
For communication among containers running on different Docker daemon hosts, you can either manage routing at the OS level, 
or you can use an overlay network.

When you start Docker, a default bridge network (also called bridge) is created automatically, 
and newly-started containers connect to it unless otherwise specified. You can also create user-defined custom bridge networks. 
User-defined bridge networks are superior to the default bridge network.

Differences between user-defined bridges and the default bridge
User-defined bridges provide automatic DNS resolution between containers.

Containers on the default bridge network can only access each other by IP addresses, unless you use the --link option, 
which is considered legacy. On a user-defined bridge network, containers can resolve each other by name or alias.

Imagine an application with a web front-end and a database back-end. If you call your containers web and db, 
the web container can connect to the db container at db, no matter which Docker host the application stack is running on.

If you run the same application stack on the default bridge network, you need to manually create links between the containers (using the legacy --link flag). 
These links need to be created in both directions, so you can see this gets complex with more than two containers which need to communicate. 
Alternatively, you can manipulate the /etc/hosts files within the containers, but this creates problems that are difficult to debug.


User-defined bridges provide better isolation.

All containers without a --network specified, are attached to the default bridge network. 
This can be a risk, as unrelated stacks/services/containers are then able to communicate.

Using a user-defined network provides a scoped network in which only containers attached to that network are able to communicate.

Containers can be attached and detached from user-defined networks on the fly.

During a container's lifetime, you can connect or disconnect it from user-defined networks on the fly. 
To remove a container from the default bridge network, you need to stop the container and recreate it with different network options.

Each user-defined network creates a configurable bridge.

If your containers use the default bridge network, you can configure it, but all the containers use the same settings, such as MTU and iptables rules. 
In addition, configuring the default bridge network happens outside of Docker itself, and requires a restart of Docker.

User-defined bridge networks are created and configured using docker network create. 
If different groups of applications have different network requirements, you can configure each user-defined bridge separately, as you create it.

Linked containers on the default bridge network share environment variables.

Originally, the only way to share environment variables between two containers was to link them using the --link flag. This type of variable sharing isn't possible with user-defined networks. However, there are superior ways to share environment variables. A few ideas:

Multiple containers can mount a file or directory containing the shared information, using a Docker volume.

Multiple containers can be started together using docker-compose and the compose file can define the shared variables.

You can use swarm services instead of standalone containers, and take advantage of shared secrets and configs.

Containers connected to the same user-defined bridge network effectively expose all ports to each other. 
For a port to be accessible to containers or non-Docker hosts on different networks, that port must be published using the -p or --publish flag.


Host network driver
If you use the host network mode for a container, that container's network stack isn't isolated from the Docker host 
(the container shares the host's networking namespace), and the container doesn't get its own IP-address allocated. 
For instance, if you run a container which binds to port 80 and you use host networking, the container's application 
is available on port 80 on the host's IP address.

Note
Given that the container does not have its own IP-address when using host mode networking, port-mapping doesn't take effect,
 and the -p, --publish, -P, and --publish-all option are ignored, producing a warning instead:

WARNING: Published ports are discarded when using host network mode
Host mode networking can be useful for the following use cases:

To optimize performance
In situations where a container needs to handle a large range of ports
This is because it doesn't require network address translation (NAT), and no "userland-proxy" is created for each port.

The host networking driver only works on Linux hosts, but is available as a Beta feature, on Docker Desktop version 4.29 and later.

You can also use a host network for a swarm service, by passing --network host to the docker service create command. 
In this case, control traffic (traffic related to managing the swarm and the service) is still sent across an overlay network,
 but the individual swarm service containers send data using the Docker daemon's host network and ports. 
This creates some extra limitations. For instance, if a service container binds to port 80, only one service container can run on a given swarm node


Overlay network driver
The overlay network driver creates a distributed network among multiple Docker daemon hosts. 
This network sits on top of (overlays) the host-specific networks, allowing containers connected to it to communicate 
securely when encryption is enabled. Docker transparently handles routing of each packet to and from the correct Docker daemon host 
and the correct destination container.

You can create user-defined overlay networks using docker network create, in the same way that you can create user-defined bridge networks.
Services or containers can be connected to more than one network at a time. 
Services or containers can only communicate across networks they're each connected to.

Overlay networks are often used to create a connection between Swarm services, 
but you can also use it to connect standalone containers running on different hosts. 
When using standalone containers, it's still required that you use Swarm mode to establish a connection between the hosts.
