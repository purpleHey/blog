---
layout: post
title:  "Today - Persisting data across container runs"
date:   2019-12-31 09:49:17 +0000
categories: docker volume
---
- [ ] Day before yesterday review
- [x] Yesterday review
{: style='list-style-type: none'}

Notes from the [Docker Mastery][docker-mastery] course section on Container lifetime.

Persisting data across container runs is done by either creating a named volume, or doing a bind mount.

# Named Volumes

You can see what volumes are created with:

`>docker volume ls`

If the Dockerfile you used to create the container has a "VOLUME /var/lib/mysql" type entry, a "Mounts" entry will be created for the container that has the mapping of the "Destination" (/var/lib/mysql) directory to the actual data "Source" directory on the local file system.  In this case it's for mysql, and when the container runs it will use the volume name on the local file system mounted at the "Source" directory.  Use `>docker container inspect <containerName>` to see the "Mounts" details.

So the container thinks it's writing to /var/lib/mysql, but in actual fact it's writing the "Source" directory.  

Its a good idea to actually name the volume when you create it because by itself, docker will create a guid type name i.e. it's hard to pick out in a line up.  Here's an example output where some of the volumes are named, and some are not:

```
➜  gitweb git:(master) ✗ docker volume ls
DRIVER              VOLUME NAME
local               48db87015dfcb89d017564cf03a2ef1b9dc84166f2af8a6458587fb8ecd888bb
local               064396c959ff1311550c89b63481caf6f6373838edf0c65b131f351e7452bdc2
local               f7ac27660a1541243da14188ad19a4a9456b5ffe8e514e0aad7c81de29a65996
local               mysql-db
local               psql-data
```

To name the volume, use -v mysql-db:/var/lib/mysql when you do the docker run.

You can `>docker volume inspect mysql-db` to get info on when the volume was created, what driver was used and what the "Mountpoint" is.

Lastly, note that on Mac's and PC's, you can't actually navigate to the Mountpoint directory because Docker is doing some sourcery with a linux VM on the machine to make this work.  On Linux you could actually navigate to that directory.

[docker-mastery]: https://www.udemy.com/course/docker-mastery
