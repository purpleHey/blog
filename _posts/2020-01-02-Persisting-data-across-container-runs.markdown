---
layout: post
title:  "Persisting data across container runs"
date:   2020-01-02 Thursday 04:51:32
categories: docker volume
---

Persisting data across container runs is done by either creating a [named volume](#named-volumes), or doing a [bind mount](#bind-mounts).  Named volumes are completely managed by Docker, so only containers that know about the volume can write to it.  This is very different from bind mount where you can mount a directory into the container, and the files on the host system are reachable and editable by other applications and processes on the host i.e. a code editor.

# Named Volumes

You can see what volumes have been created by docker:

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

To name the volume, use -v mysql-db:/var/lib/mysql on the command line, when you do the docker run, or add the volume name tag on the VOLUME stanza in docker-compose.

You can `>docker volume inspect mysql-db` to get info on when the volume was created, what driver was used and what the "Mountpoint" is.

Lastly, note that on Mac's and PC's, you can't actually navigate to the Mountpoint directory because Docker is doing some sourcery with a linux VM on the machine to make this work.  On Linux you could actually navigate to that directory.

# bind mounts

For bind mounts, use `.` or `$(PWD):/container/directory` with the `-v` option on the command line to bind the current working directory on the host to the container volume to the right of the `:`.  Note this is perfect for doing development work where you're constantly changes files and wanting them mirrored into the container to get served up to a web server through the container.

Credits: These are notes from the [Docker Mastery][docker-mastery] course section on Container lifetime by Bret Fisher.

[docker-mastery]: https://www.udemy.com/course/docker-mastery
