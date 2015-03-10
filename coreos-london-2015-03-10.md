%title: Paz - A simple Docker PaaS built on CoreOS, Etcd and Fleet
%author: @lukeb0nd luke@yld.io
%date: 2015-03-10

\                       \______  \___  \_____
                      / \___  //   |/\__  /
                     / /\__/ // /| |  / / 
                    / \_____// \___ | / /\___
                   /\_/     /\_/  |\_|/\_____/



-> # CoreOS London <-
-> ## March 2015 <-





-> Luke Bond <-
-> YLD.io <-
-> @lukeb0nd <-

---

# CONTENTS

1. Why I built Paz
2. What is Paz?
3. How does Paz use CoreOS, Etcd and Fleet?
4. Roadmap and current status of Paz

---

# WHY I BUILT PAZ

About a year ago I wanted to solve two problems:
1. To build a web front-end to CoreOS' Fleet functionality
2. Reduce the pain of multi-host Docker networking

I'll give a very brief explanation of these problems for
those who aren't familiar with Docker, CoreOS and Fleet.

---

# ETCD

* Etcd is a distributed key-value store built on a Go
  implementation of the Raft consensus algorithm
* It's something like ZooKeeper with a JSON-based REST
  interface
* You might use it to store configuration, public keys
  and for service discovery
* It's built by CoreOS and ships with CoreOS

---

# FLEET

Fleet is an abstraction to systemd at the cluster rather
than host level.

systemd is a Linux init system, like upstart, etc. You use
it to declare how services are run on a Linux system.

With systemd you say to a host "I declare service N, it is
started like so, and I want you to restart it if it dies".

With Fleet you say this to a cluster.

systemd stores its data on disk on the host. Fleet stores it
in a distributed data store called Etcd. Ultimately Fleet
boils down to just running stuff for you with systemd on
your hosts, coordinated by Etcd.

You interact with Fleet via a CLI, telling it to start and
stop services, view their logs, check their status, etc.
Just like systemd.

It's crying out for a nice web interface to do all of this.

---

# MULTI-HOST DOCKER NETWORKING

This has been a hot topic in Docker since the initial hype.

The initial reaction to Docker:
> "Wow so I can spin up reproduceable vertical stacks quickly
> and easily. Eliminating concerns about portability and host
> configuration/compatibility. [Chef|Puppet|Poverty|Cancer]
> is dead!"

Later, in production:
> "Whoa hang on a second. How the hell do I discover and link
> all these containers? And why is there still
> [Chef|Puppet|Poverty|Cancer] in the world???"

When new technology makes once-hard things easy, we very
quickly forget those hard things.

Then we blame this new technology for revealing the next
barrier!

There is a debate over whether or not the Docker tool
should be solving this problem. I refer you to the case of
Docker vs Rocket and the debate about Docker plugins.

The reality is that Docker does not currently solve multi-
host networking between containers for you out of the box.

---

# WHY I BUILT PAZ

-> So I built Paz: <-

-> A self-hosted Docker PaaS based on CoreOS and Fleet, <-
-> written in Node.js. <-


---

# WHAT IS PAZ?

* Paz is free and open-source
* Paz is your own private PaaS that you can host anywhere
* Paz runs anywhere you can run CoreOS
  * Vagrant/VirtualBox, DigitalOcean, AWS, etc.
  * Run it on your laptop or "in the cloud"
* Paz makes it easy to get started with Docker on CoreOS
* Paz is very accessible due to its simplicity
  * Underneath it's "just" Fleet and Docker
* Paz is a nice user-interface on top of Fleet
* Paz gives you zero-downtime-deployments
* Paz has a PaaS-inspired workflow designed for CD
  * i.e. Git push -> CI -> deploy
* Paz can be used like a multi-host Dokku
* Paz is designed to integrate with your CI
  * But it doesn't care which CI system you use
* Paz doesn't care what language your apps are written in
  * i.e. It runs containers

---

# WHAT IS PAZ?

* Paz is composed of:
  * Three small Node.js API services
  * Service discovery with HAProxy, Etcd and Confd
  * A beautiful Ember.js front-end designed by @jongold
  * A CLI (WIP)

---

# WHAT IS PAZ?

## Service Directory

A database of your apps that can be deployed on Paz with a
REST API. This includes fields such as service name, Docker
repository, environment variables, volume mounts, etc.

## Scheduler

Based on data about your services in the service directory,
the scheduler will use Fleet to deploy apps on the platform.
This uses a Node.js module called `fleetctl-ssh`- a Node.js
Fleet client module.

## Front-End API

A front-end API used by the website that that pulls
together parts of the scheduler and service-directory APIs
as well as info from Etcd and Fleet and also uses web-
sockets for realtime updates to the page.

## Web Front-End

Add/edit/remove services, observe their health and that of
your hosts. Access cAdvisor monitoring for your hosts and
containers.

---

# HOW DOES PAZ USE COREOS, ETCD AND FLEET?

* Paz uses Fleet to deploy services
* The scheduler writes unit files for your services from
  a template
* Each service has a sidekick unit that announces the
  service in Etcd
* Paz's service discovery solution uses HAProxy, Etcd and
  Confd
  * It gives zero-downtime-deployments and load-balancing
* Paz watches Etcd to get the same information Fleet gives
  you with `fleetctl list-units` and `fleetctl list-machines`
* If you know Fleet, you will understand Paz
* Ultimately, anything Paz does boils down to something you
  could do yourself with Fleet and Etcd
* This is one of Paz's strengths: it doesn't try to hide
  complexity from you with "magic"
* The only "magic" in Paz is the dynamic HAProxy
  configuration

---

# HOW DOES PAZ USE COREOS, ETCD AND FLEET?

* Paz supports webhooks from the Docker hub, enabling
  continuous deployment pipelines
* The scheduler also has an endpoint you can use to
  deploy manually
* If you've used Fleet before and yearned for something
  to write unit files for you and look after started
  services, Paz is your answer
* Paz can also do some basic DNS configuration for you

---

# PAZ REPOSITORIES AND LINKS

* [paz-sh/paz](https://github.com/paz-sh/paz)
  Main README file with installation scripts
* [paz-sh/paz-machine](https://github.com/paz-sh/paz-machine)
  CLI tool for provisioning a Paz cluster
* [paz-sh/paz.sh](https://github.com/paz-sh/paz.sh)
  CLI REST client tool for a running Paz cluster
* [paz-sh/paz-orchestrator](https://github.com/paz-sh/paz-orchestrator)
  The aforementioned front-end API
* [paz-sh/paz-service-directory](https://github.com/paz-sh/paz-service-directory)
  REST API for the database of services
* [paz-sh/paz-scheduler](https://github.com/paz-sh/paz-scheduler)
  Runs your services on the Paz platform using Fleet
* [paz-sh/paz-web](https://github.com/paz-sh/paz-web)
  Ember.js web front-end to Paz
* [paz-sh/paz-haproxy](https://github.com/paz-sh/paz-haproxy)
  The not-so-secret sauce powering Paz's service discovery

---

# PAZ REPOSITORIES AND LINKS

Also:
* [http://paz.sh](http://paz.sh)
  Newly-revamped landing page with links to docs
* [https://paz.readme.io](https://paz.readme.io)
  Paz documentation portal

---

# ROADMAP AND CURRENT STATUS OF PAZ

* I presented Paz at Container Camp in September 2014
* I'd planned to polish it up and release it by the end
  of 2014 but I didn't
  * I suffered from burnout and took a break
* So two weeks ago on stage at LNUG I open-sourced it as-is
* Since then I've been improving the documentation and bug-
  fixing
  * This has meant removing some half-finished features
  * Such as "environments", sadly (but it will be back)
* Installation process & documentation badly needed
  improvement

There are still some rough edges that will be smoothed
out over the coming weeks and months.

Paz is now becoming more polished and is better able to
showcase the great technology under the hood.

-----

# WANT TO GET INVOLVED?
*Please do!*

* Myself and some fellow YLD developers are working on the
  project in our spare time
* Finding contributors who know about Docker, CoreOS, Fleet,
  Etcd, and HAProxy and Confd is not easy
  * You don't have to know Node.js
* So if this interests you please start making contributions!
* We are also considering hiring someone to work on it

---



-> # LINKS <-




-> *Start here:* <-

-> `http://paz.sh` <-
-> `http://paz.readme.io` <-
-> `https://github.com/paz-sh/paz` <-

---









-> *Q* U *E* S *T* I *O* N *S* ?  <-
