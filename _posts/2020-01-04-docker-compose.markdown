---
layout: post
title:  "Today - Docker Compose - docker-compose.yml"
date:   2020-01-04 Saturday 14:59:14
categories: docker compose
---

- [ ] Day before yesterday review
- [ ] Yesterday review

`>docker-compose [up/down]` a great way to orchestrate a set of related services (containers) for local development.

{: style='list-style-type: none'}
![center-aligned-image](/assets/docker-compose.png){: .align-center}

Notes from the [Docker Mastery][docker-mastery] course section on Container lifetime.

# Docker Compose

The required parts are **version**, **service** and the associated **serviceName('s)**.
{% highlight yml %}
version: '3.1' # the version of docker-compose

service: # containers - same a docker run
  serviceName: # a friendly name.  This is also the DNS name inside the network.
    image: postgres:11
    command: # Optional - replace the default CMD specified by the image
    environment: # Optional - same as -e in >docker run
    volumes: # Optional - same as -v in >docker run
  serviceName2:

volumes: Optional - same as a >docker volume create

networks: Optional - same as a >docker network create
{% endhighlight %}

docker-compose.yml is a set of stanza's.  Note that if you are using visual studio code, and you've installed the [VSCode docker extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker), intellisense and help is at your fingertips for the different stanza's of docker-compose.

Entries in the file are either **key**: **value** pairs like image: imageName, or lists of the form:

```
  label:                  e.g.     environment:
    - var1-string                    - PASSWD=passwd
    - var2-string
```

If you use label's for the volumes in a service, you need to include those labels in a **volumes** stanza so docker knows to create those volumes before it spins up the various services.

To spin up the services is as simple as `>docker-compose up`.

[docker-mastery]: https://www.udemy.com/course/docker-mastery
