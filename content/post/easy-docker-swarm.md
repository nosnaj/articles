---
title: "Easy Docker Swarm (Part 1)"
date: 2017-08-05T11:14:12+08:00
draft: false
tags:
  - Docker
  - Docker Swarm
description: Snippets to start Docker Swarm
author: Chee Leong
---

This is a series of tutorial to guide you through setting up and deploying containers to Docker Swarm.

## Prerequisite 

* [Docker](https://www.docker.com/)
* [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
* [Compose](https://docs.docker.com/compose/)\*
* Some familiarity with Docker.

\* Comes with docker installation

For the complete guide, visit [here](https://docs.docker.com/engine/swarm/swarm-tutorial/).

## Preparation

We'll prepare 3 instances.

* manager1
* worker1
* worker2

Issue these commands

```
docker-machine create manager1
docker-machine create worker1
docker-machine create worker2
```

Issue this command to check our instances
```
docker-machine ls
``` 

You should expect response similar to this
```
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
manager1   -        virtualbox   Running   tcp://192.168.99.100:2376           v17.06.0-ce
worker1    -        virtualbox   Running   tcp://192.168.99.101:2376           v17.06.0-ce
worker2    -        virtualbox   Running   tcp://192.168.99.102:2376           v17.06.0-ce
```

## The Swarm

We're ready to set up the swarm.

`docker-machine` allows you to `ssh` directly with the assigned name.

Issue this
```
docker-machine ssh manager1
```

You should be greeted with this
```
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
docker@manager1:~$
```

As the name suggested, `manager1` is going to be our swarm leader.

Issue this to start the swarm
*If you're prompted to choose a network interface, just choose one*
```
docker swarm init
```

You should see this.
*This is what I get, your IP address might be different.*
```
Swarm initialized: current node (<yours>) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token <your token here> 192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Copy this and apply this to `worker1` and `worker2`.
If you lost this or want to do it later, issue this
```
docker swarm join-token worker
```

SSH into `worker`
```
docker-machine ssh worker1
```
And run the above docker swarm snippet.


Apply this to `worker2` too
```
docker-machine ssh worker2
```
And repeat the docker swarm snippet.

## Check your work

Now, let's get back to our `manager1` to check the node status

```
docker-machine ssh manager1
```

Run this to check if the swarm is set up properly.
```
docker node ls
```

You should be able to see this.
```
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
0tu5dt30s8yivdy30e325r8lx     worker2             Ready               Active
sph7t2578oxti521ejotyq6dy *   manager1            Ready               Active              Leader
u4y3ojg7ttps49vjm1zvh5f1m     worker1             Ready               Active
```

## The end of Part 1

And, this concludes the first part of our miniseries.

Next up, we'll deploy containers to the swarm.