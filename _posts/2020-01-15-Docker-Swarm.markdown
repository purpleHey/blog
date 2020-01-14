---
layout: post
title:  "Today - Docker Swarm"
date:   2020-01-12 Sunday 12:38:13
categories: docker swarm
published: true
---
- [ ] Day before yesterday review
- [x] Yesterday review
{: style='list-style-type: none'}

# Docker Swarm

Swarm mode is a clustering solution built into the Docker server.  In 2017 they added **stacks** and **secrets**. By default docker swarm is not initialized, and by design it does not interfere with docker.  So if you had some other orchestration mechanism, swarm would no effect it.  To turn it on you do `>docker swarm init`.

Here's the main piece parts of the **control plane** of swarm:

![center-aligned-image](/assets/swarmPP.png){: .align-center}

Manager nodes can be worker nodes, so its useful to think of them as just workers with some special permissions to control the swarm.

In a swarm, a **service** replaces the docker run command allowing us to add extra features such as replica's when it's run.  Those features are enabled as **tasks**.  A single service can have mutiple tasks, and each task can launch a separate container.

Here's a more detailed look at the piece parts that make up a manager node and a worker node:

![center-aligned-image](/assets/dockerService.png){: .align-center}

Aside: you can use `>docker info` to find out general info about your local docker config i.e. number of containers, images, version info and of course whether or not swarm is running.

When you initialize swarm it creates a single node swarm as well as a set of PKI and security automation piece parts.  It created a manager node since its the first node created, but it also:
* creates a root signing certificate for our swarm, 
* issues a certificate for the first Manager node,
* join tokens are created so nodes can join the swarm.
* initializes the RAFT consensus db that's the ledger for the swarm keeping consistency across workers in the cloud.
* RAFT DB is created to store root CA, configs and secrets,
* RAFT DB is encrypted by default on disk
* replicates logs amongst managers via mutual TLS in the control plane.

# Creating a single node swarm

Once you initialize swarm, a node is created.  You can see it by doing

{% highlight bash %}
>docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
e9fj64cz86tqhly3fa3j2zv62 *   docker-desktop      Ready               Active              Leader              19.03.5
{% endhighlight %}
Note: there is only one leader node at a time, so the first one created/initialized has the **Leader** status.  Note: you might have to scroll the code block above to see the MANAGER STATUS column.

The `>docker node cmd` commands are for promoting and demoting workers and finding their status.

## `>docker service cmd`

The service command replaces the docker run command, the docker command that's built to run a single local container.  With service, you tell the swarm what you want, and it manages all the orchestration to fulfill the requirements.

You can start a container with `>docker service create image cmd`, so for example `>docker service create alpine ping 8.8.8.8`, and docker service will start a container with alpine running the ping command.  Use `>docker service ls` to see a list of the services running.  The REPLICAS column shows #/#, which represents the number of containers requested, and the number of containers started/running.  If you want to see what's running on that service run `>docker service ps serviceName or ID`.  The NODE column lists the node where the service is running i.e. it might be on one of several machines depending on how the swarm is configured.

`>docker service update --replicas 3` will start 2 new containers (for a total of 3).  If you removed one of the containers, docker swarm's orchestration will immediately start up a 3rd one.

`>docker service rm serviceName or ID` will shut down the containers.


Notes from the [Docker Mastery][docker-mastery] course section on Container lifetime.


[docker-mastery]: https://www.udemy.com/course/docker-mastery
