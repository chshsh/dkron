---
title: Getting started
weight: 1
---

Welcome to the intro guide to dkron! This will explain how to setup dkron, how easy is to use it, what problems could it help you to solve, etc.

## Introduction

Dkron nodes can work in two modes, agents or servers.

A Dkron agent is a cluster member that can handle job executions, run your scripts and return the resulting output to the server.

A Dkron server is also a cluster member that send job execution queries to agents or other servers, so servers can execute jobs too.

The main distinction is that servers order job executions, can be used to schedule jobs, handles data storage and participate on leader election.

Dkron clusters have a leader, the leader is responsible of starting job execution queries in the cluster.

Any Dkron agent or server acts as a cluster member and it's available to run scheduled jobs.

You can choose whether a job is run on a node or nodes by specifying tags and a count of target nodes having this tag do you want a job to run. This gives an unprecedented level of flexibility in runnig jobs across a cluster of any size and with any combination of machines you need.

All the execution responses will be gathered by the scheduler and stored in the database.

## Requirements

Dkron relies on the key-value store for data storage, an instance of the distributed store can be run in the same machines as Dkron or connect it to an already existing cluster.

It is compatible with etcd, Consul, Zookeeper and Redis data stores. For instructions on how to install and configure any one of these systems refer to their official sites:

- [etcd](https://coreos.com/etcd/docs/latest/)
- [Consul](https://consul.io/intro/getting-started/install.html)
- [ZooKeeper](https://zookeeper.apache.org/doc/r3.3.3/zookeeperStarted.html)
- [Redis](https://redis.io/topics/quickstart)

## Installation

### Recommended method

APT repository: `deb [trusted=yes] https://apt.fury.io/victorcoder/ /`

Unstable release: `sudo apt-get install dkron-unstable`
Stable release: `sudo apt-get install dkron`

### Other methods

Simply download the packaged archive for your platform from the [downloads page](https://github.com/victorcoder/dkron/releases), extract the package to a shared location in your drive, like `/opt/local` and run it from there.

## Configuration

See the [configuration section](/basics/configuration).

## Usage

By default Dkron uses the following ports:

- `8946` for communicating between agents
- `8080` for HTTP for the API and Dashboard
- `6868` for RPC comunication between agents.

**Be sure you have opened this ports (or the ones that you configured) in your firewall or AWS security groups.**

By default dkron will try to use a local etcd server running in the same machine and in the default port. A different store can be specified setting `backend` and `backend-machines` flag in the config file, env variables or as a command line flag.

To start a Dkron server instance:

```
dkron agent --server
```

Time to add the first job:

{{% notice note %}}
This job will only run in just one dkron_server node due to the node count in the tag. Refer to the [target node spec](/usage/target-nodes-spec) for details.
{{% /notice %}}

```bash
curl localhost:8080/v1/jobs -XPOST -d '{
  "name": "job1",
  "schedule": "@every 10s",
  "timezone": "Europe/Berlin",
  "owner": "Platform Team",
  "owner_email": "platform@example.com",
  "disabled": false,
  "tags": {
    "dkron_server": "true:1"
  },
  "concurrency": "allow",
  "executor": "shell",
  "executor_config": {
    "command": "date"
  }
}`
```
