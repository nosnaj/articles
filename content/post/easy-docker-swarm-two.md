---
title: "Easy Docker Swarm (Part 2)"
date: 2017-08-08T18:47:16+08:00
draft: false
tags:
  - Docker
  - Docker Swarm
description: Docker Compose with Docker Swarm
author: Chee Leong
---

This is the continuation of [Part 1](../easy-docker-swarm-one/).

We'll fast track and use docker-compose.yml for our deployment.

## Prerequisite 

* [Docker](https://www.docker.com/)
* [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
* [Compose](https://docs.docker.com/compose/)\*
* Some familiarity with Docker.

\* Comes with docker installation

For the complete guide, visit [here](https://docs.docker.com/engine/swarm/swarm-tutorial/).

## Preparation

Let's create a simple `docker-compose.yml`.

```
version: "3"
services:
  web:
    image: dockercloud/hello-world
    deploy:
      replicas: 3
    ports:
      - "80:80"
```

Now let's copy this file to `manager1` so we can use it.

```
docker-machine scp docker-compose.yml manager1:
```

Now ssh into `manager1`, you should see it now.
```
$ docker-machine ssh manager1                                                            
                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/
 _                 _   ____     _            _
| |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
| '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
| |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
|_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
Boot2Docker version 17.06.0-ce, build HEAD : 0672754 - Thu Jun 29 00:06:31 UTC 2017
Docker version 17.06.0-ce, build 02c1d87
docker@manager1:~$ ls
docker-compose.yml  log.log
```

### Deployment

Now, let's deploy our compose file.

```
docker stack deploy -c docker-compose.yml helloworld
```

To check for the deployment status,

```
docker@manager1:~$ docker stack ls
NAME                SERVICES
helloworld          1

docker@manager1:~$ docker stack ps helloworld
ID                  NAME                IMAGE                            NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
k773xmtcex57        helloworld_web.1    dockercloud/hello-world:latest   worker1             Running             Running about a minute ago
ghq4nam8bncj        helloworld_web.2    dockercloud/hello-world:latest   worker2             Running             Running about a minute ago
zhi5r9pg334t        helloworld_web.3    dockercloud/hello-world:latest   manager1            Running             Running about a minute ago
```

If you see the above, congratulation, you deployed 3 instances of `dockercloud/hello-world`.

Let's examine if these are working.

Let's get our IP from `docker-machine`.
```
$ docker-machine ls                                                                                                                                [19:27:57]
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
manager1   -        virtualbox   Running   tcp://192.168.99.100:2376           v17.06.0-ce
worker1    -        virtualbox   Running   tcp://192.168.99.101:2376           v17.06.0-ce
worker2    -        virtualbox   Running   tcp://192.168.99.102:2376           v17.06.0-ce
```

Because our container is bound to the port 80, let's just copy and paste the IP into our browser. In my case, I chose `192.168.99.100`.

You should be able to see this now.

![Hello World](/img/easy-docker-swarm-two/hello.png)

Congratulations! You have a working Docker Swarm.

## Bonus

The 3 instances work as round robin.

Because of browser cache, you might want to use `curl` for it.

```
curl http://192.168.99.100
```

If you looked at the body, you'll find hostname at the body to be rotated.

These are what I got

```
<h3>My hostname is b7d84eaba105</h3>

<h3>My hostname is 220a42dfd644</h3>

<h3>My hostname is c6f44189b0f5</h3>
```

That concludes our quick snippets, for more details on how to use Docker Swarm and Docker Compose, visit the official website included above this article.
